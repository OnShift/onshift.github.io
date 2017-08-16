---
layout: post
authors:
 - ilyaGotfryd
 - aliOrlando
date: 2017-08-16 01:30:00 -0500
title: Testing and infrastructure&#58; Refactoring a Legacy Application in React - Part 3
test_tools:
 - image_path: /images/karma_icon.png
   tool_name: Karma
   tool_description: A trusty test runner that provides multiple browsers including PhantomJS and various pre-processing plugins.
   tool_url: https&#58;//karma-runner.github.io/1.0/index.html
 - image_path: /images/mocha_icon.png
   tool_name: Mocha
   tool_description: Feature-rich JavaScript test framework running on Node.js
   tool_url: https&#58;//mochajs.org/
 - image_path: /images/chai_icon.png
   tool_name: Chai
   tool_description: BDD / TDD assertion library for node and the browser that can be delightfully paired with any javascript testing framework.
   tool_url: http&#58;//chaijs.com/
---


*Note: This post is part of a series, starting with [Refactoring a Legacy Application with React - Introduction]({% post_url 2017-08-14-Refactoring-with-reactJS-introduction %})*

In this post we are going to cover the essential part that makes move to React as a front-end framework so highly desirable. We are talking of course about testing.

# Topics we cover
  * Tool set with short description of game pieces and choices
  * Homegrown helper utilities
  * Basic DOM rendering tests
  * Tests of changes in DOM rendering though events and state changes
  
## Tool set
We start of with actual tools that we are using in our legacy stack. Our organization had prior in-depth front-end work 
done in our legacy application. We have had a set of tools already in use and we decided to continue the trend and reuse
things that have been deployed. That being said you might pick a different set of tools especially if you are starting
with testing completely a new.<br />
**So here is our tool set:**
{% assign toolz = page.test_tools %}
<div class="container">
<ul>
    {% for tool in toolz %}
        <il> <div class="row">
        <div class="col-md-1">
        <a href="{{tool.tool_url}}"><img class="tool-icon" src="{{ tool.image_path }}"  /></a>
        </div><div class="col-md-11">
        {{ tool.tool_name }} - {{ tool.tool_description }}
        </div></div>
        </il>
    {% endfor %}
</ul>
</div>

