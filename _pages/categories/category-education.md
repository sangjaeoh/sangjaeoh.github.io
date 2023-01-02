---
title: "education"
layout: archive
permalink: categories/education
author_profile: true
sidebar_main: true
sidebar:
    nav: "docs"
---


{% assign posts = site.categories['education'] %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}