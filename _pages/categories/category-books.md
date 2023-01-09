---
title: "books"
layout: archive
permalink: categories/books
author_profile: true
sidebar_main: true
sidebar:
    nav: "docs"
---


{% assign posts = site.categories['books'] %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}