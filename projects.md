---
layout: default
title: Projects
permalink: /projects/
---

# Projects

<ul class="post-list">
{% assign sorted_projects = site.projects | sort: "order" %}
{% for project in sorted_projects %}
  <li>
    <h2 class="post-title"><a href="{{ project.project_url }}">{{ project.title }}</a></h2>
    <p class="post-excerpt">{{ project.excerpt }}</p>
  </li>
{% endfor %}
</ul>
