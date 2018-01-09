---
permalink: /projects/
title: Projects
---
{% for post in site.categories.Project %}
 <li><span>{{ post.date | date_to_string }}</span> &nbsp; <a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}