---
title: "Kubernetes"
layout: archive
permalink: categories/kubernetes
author_profile: true
sidebar_main: true
sidebar:
    nav: "docs"
---


{% assign posts = site.categories['Kubernetes'] %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}