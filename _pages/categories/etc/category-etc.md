---
title: "기타"
layout: archive
permalink: categories/etc
author_profile: true
sidebar_main: true
sidebar:
    nav: "docs"
---


{% assign posts = site.categories['etc'] %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}