---
layout: page
permalink: /publications/
title: publications
description: Manuscripts, peer-reviewed papers, and conference presentations.
nav: false
nav_order: 2
---

<!-- _pages/publications.md -->

<!-- Bibsearch Feature -->

{% include bib_search.liquid %}

<div class="publications">

<h2>Manuscripts Under Review and Submitted</h2>

{% bibliography --query @unpublished %}

<h2>Accepted Publications and Presentations</h2>

{% bibliography --query !@unpublished %}

</div>

<script>
  window.addEventListener('DOMContentLoaded', () => {
    document.querySelectorAll('.publications .links a[href^="/projects/"]').forEach((link) => {
      const title = link.closest('li')?.querySelector('.title')?.textContent.trim() || 'project';
      link.textContent = 'Project';
      link.setAttribute('aria-label', `Open project page: ${title}`);
    });
  });
</script>
