---
layout: page
title: About Us
path: /about
permalink: /about
sidenav:
  -
    title: Our True North
    path: '/about#truenorth'
  -
    title: Dedicated To Care
    path: '/about#dedication'
  -
    title: Blog Authors
    path: '/about#authors'
---

<h1 id="about" class="h2">About OnShift</h1>
<div class="card mt-2">
  <div class="card-body">
    <h3 class="card-title" id="truenorth">Our True North</h3>
    <p class="card-text">To be the leading provider of strategic Human Capital Management software and services to Healthcare, through continuous delivery of value to the marketplace at a sustained and predictable pace.</p>
  </div>
</div>

<div class="card mt-2 mb-3">
  <div class="card-body">
    <h3 class="card-title" id="dedication">Dedicated To Long-Term Care And Senior Living</h3>
    <p class="card-text">OnShift delivers cloud-based software and proactive services to solve everyday workforce challenges in healthcare. Our suite of products for hiring, scheduling and workforce analysis drives quality care, lower costs and higher performance by empowering providers to staff consistently and efficiently.</p>
    <p class="card-text">Intuitive design, predictive analytics and customer success management are why thousands of post-acute care and senior living organizations rely on OnShift. We focus exclusively on the long-term care and senior living industry. This means that our software and services are built upon an in-depth understanding of how providers work day-in and day-out, and how industry regulations and issues affect their every move.</p>
  </div>
</div>

<h1 id="authors" class="h2">About the Authors</h1>

<div class="authors">
  {% for author_hash in site.data.people %}
  {% assign author = author_hash[1] %}
    <article class="author card pa-3 mt-2" id="author-{{ author_hash[0] }}">
      <div class="flex flex-wrap flex-center">
        <div class="flex-0-auto pr-sm">
          <div class="img-circle img-circle-xl" style="background-image: url({% if author.avatar %}{{ author.avatar }}{% else %}https://upload.wikimedia.org/wikipedia/commons/7/7c/Profile_avatar_placeholder_large.png{% endif %});"></div>
        </div>
        <div class="flex-1-auto">
          <h3 class="mt-0">{{ author.full_name }}</h3>
          <h4 class='mt-sm'>{{ author.job_title }}</h4>
          <div class='mt-sm'>
            {% if author.handles.github %}
            <a href="http://github.com/{{ author.handles.github }}"><i class="svg-icon github"></i></a>
            {% endif %}
            {% if author.handles.linkedin %}
            <a href="https://www.linkedin.com/in/{{ author.handles.linkedin }}/"><i class="svg-icon linkedin"></i></a>
            {% endif %}
            {% if author.handles.twitter %}
            <a href="https://twitter.com/{{ author.handles.twitter }}"><i class="svg-icon twitter"></i></a>
            {% endif %}
          </div>
        </div>
      </div>

      <div class="bio mt-sm">
        <p>
          {{ author.bio }}
        </p>
      </div>
    </article>
  {% endfor %}
</div>
