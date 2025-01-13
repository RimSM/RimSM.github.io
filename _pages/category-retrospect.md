--- 
title: "Retrospect"
layout: archive
permalink: /retrospect/
author_profile: true
sidebar: 
    nav: "sidebar-category"
---
{% assign posts = site.categories.retrospect %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
