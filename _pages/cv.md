---
layout: page
permalink: /cv/
title: CV
nav: true
nav_order: 5
cv_pdf: /assets/rendercv/rendercv_output/Yiwei_Shu_CV.pdf
cv_format: rendercv # options: rendercv, jsonresume
cv_photo: /assets/img/profile-cv.jpg
description: Academic robotics CV for Yiwei Shu.
toc:
  sidebar: left
---

<style>
  @media (min-width: 992px) {
    .container.mt-5:has(.cv) {
      width: calc(100% - 48px);
      max-width: 1200px;
    }
  }
</style>

{% assign cv = site.data.cv.cv %}

<p class="text-right">
  <a href="{{ page.cv_pdf | relative_url }}" target="_blank" rel="noopener noreferrer" class="btn btn-sm z-depth-0">
    <i class="fa-solid fa-file-pdf"></i> PDF
  </a>
</p>

<div class="cv">
  <div class="card mt-3 p-3">
    <div class="row align-items-center">
      {% if page.cv_photo %}
        <div class="col-12 col-sm-3 text-center mb-3 mb-sm-0">
          <img
            src="{{ page.cv_photo | relative_url }}"
            alt="Portrait of {{ cv.name }}"
            class="img-fluid rounded z-depth-1"
            style="max-height: 220px; object-fit: cover"
          >
        </div>
      {% endif %}
      <div class="col-12 {% if page.cv_photo %}col-sm-9{% endif %}">
        <h3 class="card-title font-weight-medium">Contact Information</h3>
        <table class="table table-cv table-sm table-borderless mb-0">
          <tr>
            <td class="p-1 pr-2 font-weight-bold"><b>Name</b></td>
            <td class="p-1 pl-2 font-weight-light">{{ cv.name }}</td>
          </tr>
          {% if cv.headline %}
            <tr>
              <td class="p-1 pr-2 font-weight-bold"><b>Professional Title</b></td>
              <td class="p-1 pl-2 font-weight-light">{{ cv.headline }}</td>
            </tr>
          {% endif %}
          {% if cv.email %}
            <tr>
              <td class="p-1 pr-2 font-weight-bold"><b>Email</b></td>
              <td class="p-1 pl-2 font-weight-light"><a href="mailto:{{ cv.email }}">{{ cv.email }}</a></td>
            </tr>
          {% endif %}
          {% if cv.location %}
            <tr>
              <td class="p-1 pr-2 font-weight-bold"><b>Location</b></td>
              <td class="p-1 pl-2 font-weight-light">{{ cv.location }}</td>
            </tr>
          {% endif %}
          {% if cv.website %}
            <tr>
              <td class="p-1 pr-2 font-weight-bold"><b>Website</b></td>
              <td class="p-1 pl-2 font-weight-light"><a href="{{ cv.website }}">{{ cv.website }}</a></td>
            </tr>
          {% endif %}
        </table>
      </div>
    </div>
  </div>

{% for section_pair in cv.sections %}
{% assign section_title = section_pair[0] %}
{% assign section_entries = section_pair[1] %}
{% if section_title == 'Projects' or section_title == 'Experience' or section_title == 'Research Profile' %}
{% continue %}
{% endif %}

    <a class="anchor" id="{{ section_title | slugify }}"></a>
    <div class="card mt-3 p-3">
      <h3 class="card-title font-weight-medium">{{ section_title }}</h3>
      <div>
        {% if section_title == 'Education' %}
          {% assign entries = section_entries %}
          {% include cv/education.liquid %}
        {% elsif section_title == 'Manuscripts Under Review' %}
          <div class="publications">
            {% bibliography --query @unpublished %}
          </div>
        {% elsif section_title == 'Publications' %}
          <div class="publications">
            {% bibliography --query !@unpublished %}
          </div>
        {% elsif section_title == 'Manuscripts Under Review and Submitted' or section_title == 'Publications and Presentations' %}
          {% assign entries = section_entries %}
          {% include cv/publications.liquid %}
        {% else %}
          {% if section_entries[0].bullet %}
            <ul class="card-text font-weight-light list-group list-group-flush">
              {% for entry in section_entries %}
                <li class="list-group-item">{{ entry.bullet | markdownify | remove: '<p>' | remove: '</p>' }}</li>
              {% endfor %}
            </ul>
          {% else %}
            {% for entry in section_entries %}
              {% if entry.label %}
                <div class="card-text font-weight-light mb-2"><strong>{{ entry.label }}:</strong> {{ entry.details }}</div>
              {% endif %}
            {% endfor %}
          {% endif %}
        {% endif %}
      </div>
    </div>

{% endfor %}

</div>

<script>
  window.addEventListener('DOMContentLoaded', () => {
    document.querySelectorAll('.cv .publications .links a[href^="/projects/"]').forEach((link) => {
      const title = link.closest('li')?.querySelector('.title')?.textContent.trim() || 'project';
      link.textContent = 'Project';
      link.setAttribute('aria-label', `Open project page: ${title}`);
    });
  });
</script>
