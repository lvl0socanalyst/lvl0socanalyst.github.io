---
layout: default
---

<h2>Project Work</h2>
<ul>
  {% for post in site.categories.project %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

<h2>Malware Analysis</h2>
<ul>
  {% for post in site.categories.malware analysis %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

<h2>Threat Intel</h2>
<ul>
  {% for post in site.categories.threat intel %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

<h2>Privacy</h2>
<ul>
  {% for post in site.categories.privacy %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

<h2>Tutorials</h2>
<ul>
  {% for post in site.categories.tutorials %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

<h2>Scripts</h2>
<ul>
  {% for post in site.categories.scripts %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
