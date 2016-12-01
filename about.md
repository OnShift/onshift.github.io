---
layout: page
title: About Us
permalink: /about/
---

## Our True North

To be the leading provider of strategic Human Capital Management software and services to Healthcare, through continuous delivery of value to the marketplace at a sustained and predictable pace.

### Dedicated To Long-Term Care And Senior Living
OnShift delivers cloud-based software and proactive services to solve everyday workforce challenges in healthcare. Our suite of products for hiring, scheduling and workforce analysis drives quality care, lower costs and higher performance by empowering providers to staff consistently and efficiently. Intuitive design, predictive analytics and customer success management are why thousands of post-acute care and senior living organizations rely on OnShift. We focus exclusively on the long-term care and senior living industry. This means that our software and services are built upon an in-depth understanding of how providers work day-in and day-out, and how industry regulations and issues affect their every move.

## About the Authors

<div class="authors">
  {% for author_hash in site.data.people %}
  {% assign author = author_hash[1] %}
    <article class="author" id="author-{{ author_hash[0] }}">

      <h2>{{ author.full_name }}</h2>
      <h3>{{ author.job_title }}</h3>

      <div class="bio">
        {{ author.bio }}
      </div>
    </article>
  {% endfor %}
</div>
