---
layout: post
authors:
    - ilyaGotfryd
    - aliOrlando
date: 2017-08-08 10:00:00 -0500
title: Refactoring a Legacy Application with React - Introduction
edits:
  - date: 2017-08-18 17:00:00 -0500
    description: Added link to Part 2
---

Are you working through wads of unruly JavaScript that accumulated in your web app over the years? Would you like to bring in a concise and testable framework into its place?

A common complication a developer experiences when looking to on-board a new frontend framework is how difficult it is to do so incrementally. In this 4-part series of posts, we would like to describe the journey our team underwent to make that a reality in our Python backed monolith project.

# About the Series
In this post, we explain some background and context around the legacy application we want to improve upon and why we chose React. If you want to jump into the approach, you can jump in to one of the relevant series topics:
* [Part 1: Components Inside Out]({% post_url 2017-08-07-Refactoring-with-reactJS-components-inside-out %})
* [Part 2: Shared State]({% post_url 2017-08-18-Refactoring-with-reactJS-shared-state %})
* Part 3: Unit Testing - Coming Soon!

## What we are going to cover

In the following several posts we will document the following journey:
* Starting with small components and refactoring out
* Attaching React components in the DOM with jQuery
* Managing shared state between React components and legacy code
* Bundling, transpiling, and including the new React code on our pages
* Testing our new React components

# Why refactor?

The first thing we want to address in this series is why we want to refactor. Our motivation comes from maintaining a large monolith project that uses server-rendered templates with embedded python code to generate client HTML views.

Over the years, dynamic behavior has been added to client pages with a massive amount of spaghetti jQuery code. The code relies on jQuery selectors and DOM manipulation frequently selecting on CSS classes. It's scattered across multiple JavaScript files loosely related to pages. Restyling efforts have become difficult since changing class names on elements can actually break functionality that we didn't even know about. To make matters worse, there's a ton of duplicate code copy/pasted across files with little ability to reuse functionality. Not to mention the complete lack of front end unit testing.

At this point, the most optimistic among us might say "Let's just do a rewrite!" And we could subsequently spend months and months trying to achieve feature parity for an application that generates most of our company's revenue while somehow still maintaining the product until we can release the rewrite. Then we'd have to find a new product owner because we drove this one to a psychotic break. Poor fella.

While our description might be a bit dramaticized, you don't have to take just our word on application rewrites. The dangers and pitfalls of software rewrites [has](http://onstartups.com/tabid/3339/bid/2596/Why-You-Should-Almost-Never-Rewrite-Your-Software.aspx) [been](https://steveblank.com/2011/01/25/startup-suicide-%E2%80%93-rewriting-the-code/) [written](https://codeahoy.com/2016/04/21/when-to-rewrite-from-scratch-autopsy-of-a-failed-software/) [about](https://jacquesmattheij.com/improving-a-legacy-codebase) [extensively](https://storify.com/jrauser/on-the-big-rewrite-and-bezos-as-a-technical-leader).

The point being that a rewrite is way out of scope for a heavily used, successful product - even when we're struggling with technical debt. This is why an incremental refactoring approach that allows us to write reusable, testable components is exactly what we're looking for.

## Refactor Goals
* **Incremental Refactoring**
    We want the ability to grow our refactoring incrementally. This way we can narrow our React changes down to individual stories. This allows us to fulfill the requirements of Product Owners and other stakeholders while progressivly removing tech debt and improving clarity.

* **Front end testability**
    Using React.JS framework allows us to create code that will be fully tested and testable with an abundance of HOW-TO documentation out of the gate.

* **UI consistency and code resuse**
    During development of our greenfield React project, we created a repository of generic components that could be reused for common layout and display purposes. Bringing React into our legacy application means that we get to use these components and start creating consistent UI patterns across out application suite. It also helps improve maintainability of common UI elements in the application through code reuse.

* **Decreasing load on server**
    As we are moving our DOM rendering to the client, we will no longer spend server time constructing partial views and sending them to the customer. This will lower the consistent web traffic and allow us to slowly shift our monolith to become a REST service.

* **Build with “shiny” battle hardened tools similar across OnShift**
    React.JS is an industry accepted UI microframework that is heavily used and supported by a large community. OnShift has newer offerings built using this technology and we want to leverage all of tech findings in our legacy application without engaging in a major frontend rewrite.

# Let's Do It

We're excited to dive into the approach we took in our refactoring efforts, and we hope you might be able to benefit from them, too. Just keep in mind that refactoring a legacy application can be a long process. Make sure you address what you want out of a refactor effort before committing to a new pattern.

Now, let's get started in [Components Inside Out: Refactoring a Legacy Application with React - Part 1]({% post_url 2017-08-07-Refactoring-with-reactJS-components-inside-out %})
