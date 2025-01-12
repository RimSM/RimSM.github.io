--- 
title: "Book"
layout: archive
permalink: /book
author_profile: true
sidebar: 
    nav: "sidebar-category"
---
{% assign posts = site.categories.book %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
