---
layout: page
title: About Us
path: /about/
permalink: /about/
---

## Our True North

To be the leading provider of strategic Human Capital Management software and services to Healthcare, through continuous delivery of value to the marketplace at a sustained and predictable pace.

## Dedicated To Long-Term Care And Senior Living
OnShift delivers cloud-based software and proactive services to solve everyday workforce challenges in healthcare. Our suite of products for hiring, scheduling and workforce analysis drives quality care, lower costs and higher performance by empowering providers to staff consistently and efficiently. Intuitive design, predictive analytics and customer success management are why thousands of post-acute care and senior living organizations rely on OnShift. We focus exclusively on the long-term care and senior living industry. This means that our software and services are built upon an in-depth understanding of how providers work day-in and day-out, and how industry regulations and issues affect their every move.

## About the Authors

<div class="authors">
  {% for author_hash in site.data.people %}
  {% assign author = author_hash[1] %}
    <article class="author mt-md" id="author-{{ author_hash[0] }}">

      <div class="row">
        <div class="col-sm-2">
          <div class="img-circle img-circle-xl" style="background-image: url({% if author.avatar %}{{ author.avatar }}{% else %}https://upload.wikimedia.org/wikipedia/commons/7/7c/Profile_avatar_placeholder_large.png{% endif %});"></div>
        </div>
        <div class="col-sm-10">
          <h3>{{ author.full_name }}</h3>
          <h4>{{ author.job_title }}</h4>
        </div>
      </div>

      <div class="bio mt-md">
        <p>
          {{ author.bio }}
        </p>
      </div>
    </article>
  {% endfor %}
</div>
