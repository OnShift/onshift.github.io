---
layout: default
title: About Us
path: /about
permalink: /about
---

<h1 id="about" class="mb-2">About OnShift</h1>

<h2 id="truenorth">Our True North</h2>
<p>To be the leading provider of strategic Human Capital Management software and services to Healthcare, through continuous delivery of value to the marketplace at a sustained and predictable pace.</p>

<h2 id="dedication">Dedicated To Long-Term Care And Senior Living</h2>
<p>OnShift delivers cloud-based software and proactive services to solve everyday workforce challenges in healthcare. Our suite of products for hiring, scheduling and workforce analysis drives quality care, lower costs and higher performance by empowering providers to staff consistently and efficiently.</p>
<p>Intuitive design, predictive analytics and customer success management are why thousands of post-acute care and senior living organizations rely on OnShift. We focus exclusively on the long-term care and senior living industry. This means that our software and services are built upon an in-depth understanding of how providers work day-in and day-out, and how industry regulations and issues affect their every move.</p>

<h1 id="authors" class="mt-4">About the Authors</h1>

<div class="authors">
  {% for author_hash in site.data.people %}
  {% assign author = author_hash[1] %}
    <div class="author card p-3 mt-2" id="author-{{ author_hash[0] }}">
      <div class="d-flex flex-wrap flex-center">
        <div class="flex-0-auto pr-2">
          <img class="rounded-circle" width="70" height="70" src="{% if author.avatar %}{{ author.avatar }}{% else %}https://upload.wikimedia.org/wikipedia/commons/7/7c/Profile_avatar_placeholder_large.png{% endif %}"/>
        </div>
        <div class="flex-1-auto">
          <h3 class="mt-0">{{ author.full_name }}</h3>
          <h4 class='mt-1'>{{ author.job_title }}</h4>
        </div>
        <div class='mt-sm flex-0-auto'>
          <div class="p-2">
            {% if author.handles.github %}
            <a href="http://github.com/{{ author.handles.github }}"><i class="fa fa-2x fa-github github"></i></a>
            {% endif %}
            {% if author.handles.linkedin %}
            <a href="https://www.linkedin.com/in/{{ author.handles.linkedin }}/"><i class="fa fa-2x fa-linkedin-square linkedin"></i></a>
            {% endif %}
            {% if author.handles.twitter %}
            <a href="https://twitter.com/{{ author.handles.twitter }}"><i class="fa fa-2x fa-twitter-square twitter"></i></a>
            {% endif %}
          </div>
        </div>
      </div>

      <div class="bio mt-sm">
        <p>
          {{ author.bio }}
        </p>
      </div>
    </div>
  {% endfor %}
</div>
