--- 
title: "Small_talk"
layout: archive
permalink: /small_talk
author_profile: true
sidebar: 
    nav: "sidebar-category"
---
{% assign posts = site.categories.small_talk %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
