--- 
title: "Retrospect"
layout: archive
permalink: /retrospect
---
{% assign posts = site.categories.retrospect %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
