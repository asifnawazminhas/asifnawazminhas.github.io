---
title: Attack Surface Map
icon: fas fa-sitemap
order: 5
permalink: /attack-surface-map/
layout: page
---

<style>
/* 1) Remove the "embedded page" chrome for THIS page */
main .breadcrumb,
main h1,
main .page-heading,
main .post-title,
main header,
main .topbar,
main .content-header {
  display: none !important;
}

/* 2) Remove page padding so the iframe can go edge-to-edge */
.page-content,
main,
#main-wrapper,
#main,
#main > .container,
#main > .container-fluid {
  padding: 0 !important;
  margin: 0 !important;
  max-width: 100% !important;
}

/* 3) Fullscreen iframe */
.attackmap-embed {
  width: 100%;
  height: 100vh;
  border: 0;
  display: block;
}

/* If you still get a tiny outer scroll, this helps */
html, body {
  overflow: hidden;
}
</style>

<iframe
  class="attackmap-embed"
  src="/assets/web-security-attack-surface-map/"
  loading="lazy"
  title="Web Security Attack Surface Map"
></iframe>
