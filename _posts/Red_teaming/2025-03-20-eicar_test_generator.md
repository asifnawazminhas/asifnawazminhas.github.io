---
title: "Generating 68 KB EICAR Test Files in Multiple Formats"
date: 2025-03-20 10:10:00 +0000
categories: [Red Teaming]
tags: [EICAR, antivirus, security-testing, malware-simulation]
pin: false
---

# Generating 68 KB EICAR Test Files in Multiple Formats

The **EICAR test file** is a standardized file used to test antivirus detection without using actual malware. This repository provides a script to generate **EICAR test files** of exactly **68 KB** in various formats, including **TXT, PDF, XLS/XLSX, and ZIP**.

🔗 **[GitHub Repository: EICAR 68KB Tester](https://github.com/asifnawazminhas/eicar-68kb-tester){:target="_blank"}**  

---

## Why Use This Tool?

This tool is useful for:
-  **Antivirus detection testing**
- **Security research & malware simulations**
- **Network monitoring validation**
- **CSP & file restriction testing**

Unlike standard EICAR test files, which are typically **68 bytes**, this generator ensures the file is **exactly 68 KB**, making it ideal for testing **file upload restrictions, scanning mechanisms, and storage limitations**.

---

## Installation & Usage

### **1️⃣ Install Dependencies**
Run the following command to install the required dependencies:
```bash
pip install fpdf pandas openpyxl xlwt
```
Or install everything from the requirements.txt file:

```bash
pip install -r requirements.txt
```

2️⃣ Run the Script

Execute the script to generate a 68 KB EICAR test file:
```bash
python3 generate_eicar_file.py
```
3️⃣ Choose a File Format

You will be prompted to choose the format:

```bash
Choose the format in which the 68 KB EICAR test file should be generated:
1. txt
2. pdf
3. xls
4. xlsx
5. zip
```

Enter the number (1-5) for format selection:

After selecting an option, the EICAR test file will be created in the current directory.

📏 Verifying the File Size

To ensure the file size is exactly 68 KB, use one of the following methods:

🔹 Check with ls (Linux/macOS):
```bash
ls -lh eicar_test_68kb.*
```

🔹 Check with exiftool (For PDFs and structured files):
```bash
exiftool eicar_test_68kb.pdf
```

About the EICAR Test File

Any antivirus product that supports the EICAR test file should detect it in any file that starts with the first 68 characters and is exactly 68 bytes long:

```bashX5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*```

The first 68 characters form the known EICAR string. Optionally, whitespace characters may be appended, but the total length must not exceed 128 characters.

🔗 **Reference:** [EICAR Official Website](https://www.eicar.org/download-anti-malware-testfile/){:target="_blank"}

## 📄 License
This project is released under the **MIT License**.
