---
layout: post
author: dschaefer
date: 2017-04-02 15:00:00 -0500
title: Adding a docker-compose File for the Blog
---

We haven't had much traction with the blog since its inception in December. So, I thought I'd make it a little easier for the people in our Engineering Department to contribute.

Today, I have created a `docker-compose.yml` file. Having done this allows contributors to not have to worry about dependencies, such as ruby, the gems that jekyll requires, and other inconvenient hassles previously required to contribute. Since most of our newer projects require docker for local development, that dependency should already be installed on our developers' machines. The `docker-compose.yml` file takes care of the rest.

As of this writing, the contents of the `docker-compose.yml` file is as follows. I have jekyll serving drafts and unpublished articles as well, because contributions should start as drafts anyway.

~~~ yaml
jekyll:
    image: jekyll/jekyll:pages
    command: rm -rf ./_site/*
    command: jekyll serve --watch --incremental --drafts --unpublished --future --verbose
    ports:
        - 4000:4000
    volumes:
        - .:/srv/jekyll
~~~
