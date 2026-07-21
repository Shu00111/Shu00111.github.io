---
layout: page
permalink: /cv/
title: CV
nav: false
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
          {% if cv.phone %}
            <tr>
              <td class="p-1 pr-2 font-weight-bold"><b>Phone</b></td>
              <td class="p-1 pl-2 font-weight-light">{{ cv.phone }}</td>
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
        {% elsif section_title == 'Research Experience' %}
          {% assign entries = section_entries %}
          {% include cv/experience.liquid %}
        {% elsif section_title == 'Leadership and Service' %}
          <ul class="card-text font-weight-light list-group list-group-flush">
            {% for entry in section_entries %}
              <li class="list-group-item">
                <div class="row">
                  <div class="col-xs-2 col-sm-2 col-md-2 text-center date-column">
                    {% if entry.date %}
                      {% assign service_date = entry.date %}
                    {% else %}
                      {% assign service_start = entry.start_date | split: '-' | first %}
                      {% assign service_end = entry.end_date | split: '-' | first %}
                      {% if service_end == 'present' %}
                        {% assign service_end = 'Present' %}
                      {% endif %}
                      {% assign service_end = service_end | default: 'Present' %}
                      {% assign service_date = service_start | append: ' - ' | append: service_end %}
                    {% endif %}
                    <span class="badge font-weight-bold danger-color-dark text-uppercase align-middle" style="min-width: 75px">
                      {{ service_date }}
                    </span>
                  </div>
                  <div class="col-xs-10 col-sm-10 col-md-10 mt-2 mt-md-0">
                    <h6 class="title font-weight-bold ml-1 ml-md-4">{{ entry.position }}</h6>
                    <h6 class="ml-1 ml-md-4" style="font-size: 0.95rem">{{ entry.company }}</h6>
                    {% if entry.highlights %}
                      <ul class="items">
                        {% for item in entry.highlights %}
                          <li>
                            <span class="item">{{ item | markdownify | remove: '<p>' | remove: '</p>' }}</span>
                          </li>
                        {% endfor %}
                      </ul>
                    {% endif %}
                  </div>
                </div>
              </li>
            {% endfor %}
          </ul>
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
