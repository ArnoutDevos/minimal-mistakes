---
permalink: /projects/
title: Projects
---
Projects I have worked on are listed below.

{% for post in site.categories.Project %}
 <li><span>{{ post.date | date_to_string }}</span> &nbsp; <a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}