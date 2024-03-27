---
title: "AI"
layout: archive-single
permalink: /ai
author_profile: true
sidebar:
    nav: "sidebar-category"
---

{% assign posts = site.categories.AI %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
