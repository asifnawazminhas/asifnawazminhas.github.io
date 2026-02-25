---
title: Attack Surface Map
icon: fas fa-sitemap
order: 6
permalink: /attack-surface-map/
layout: page
---

<style>
/* Make the page use maximum width and reduce theme padding */
.page-content {
  padding-left: 0 !important;
  padding-right: 0 !important;
}

/* Make iframe fill the viewport area nicely */
.attackmap-embed {
  width: 100%;
  height: calc(100vh - 140px);
  border: 0;
  display: block;
}

/* On smaller screens, give a bit more room */
@media (max-width: 768px) {
  .attackmap-embed {
    height: calc(100vh - 110px);
  }
}
</style>

<iframe
  class="attackmap-embed"
  src="/assets/web-security-attack-surface-map/"
  loading="lazy"
  title="Web Security Attack Surface Map"
></iframe>
