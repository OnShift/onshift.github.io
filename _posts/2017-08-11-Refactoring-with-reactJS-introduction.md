---
layout: post
authors:
    - ilyaGotfryd
    - aliOrlando
date: 2017-08-08 13:30:00 -0500
title: Refactoring a Legacy Application with React - Introduction
---

Are you working through wads of unruly JavaScript that accumulated in your web app over the years? Would you like to bring in a concise and testable framework into its place?

A common complication a developer experiences when looking to on-board a new frontend framework is how difficult it is to do so incrementally. In this 4-part series of posts, I would like to describe the journey our team underwent to make that a reality in our Python backed monolith project.

In this post, we explain come background and context around the legacy application we want to improve upon and why we chose React. If you want to jump into the approach, you can get started in [Components Inside Out: Refactor a Legacy Application in React - Part 1]({% post_url 2017-08-07-Refactoring-with-reactJS-components-inside-out %}).

# What we are going to cover

In the following several posts we will document the following journey:
* Starting with small components and refactoring out
* Attaching React components in the DOM with jQuery
* Managing shared state between React components and legacy code
* Bundling, transpiling, and including the new React code on your pages
* Testing your new React components

# _Why_ are you doing this?

The first thing we want to address in this series is why we want to refactor. Our motivation comes from maintaining a large monolith project that uses server-rendered templates with embedded python code to generate client HTML views.

Over the years, dynamic behavior has been added to client pages with a massive amount of spaghetti jQuery code. The code relies on jQuery selectors and DOM manipulation frequently selecting on CSS classes. It's scattered across multiple JavaScript files loosely related to pages. Restyling efforts have become difficult since changing class names on elements can actually break functionality that we didn't even know about. To make matters worse, there's a ton of duplicate code copy/pasted across files with little ability to reuse functionality. Not to mention the complete lack of front end unit testing.

At this point, the most optimistic among us might say "Let's just do a rewrite!" And you could subsequently spend months and months trying to achieve feature parity for an application that generates most of your company's revenue while somehow still maintaining the product until you can release your rewrite. Then you'd have to find a new product owner because you drove the last one to a psychotic break. Poor fella.

The point being that a rewrite is way out of scope for a heavily used, successful product - even when we're struggling with technical debt. This is why an incremental refactoring approach that allows us to write reusable, testable components is exactly what we're looking for.

## In Summary, we want:
* **Incremental Refactoring**
    We want the ability to grow our refactoring incrementally. This way we can narrow our React changes down to individual stories. This allows us to fulfill the requirements of Product Owners and other stakeholders while progressivly removing tech debt and improving clarity.

* **Front end testability**
    Using React.JS framework allows us to create code that would be fully tested and testable with an abundance of HOW-TO documentation out of the gate.

* **UI consistency and code resuse**
    During development of our greenfield React project, we created a repository of generic components that could be reused for common layout and display purposes. Bringing React into our legacy application means that we get to use these components and start creating consistent UI patterns across out application suite. It also helps improve maintainability of common UI elements in the application through code reuse.

* **Decreasing load on server**
    As we are moving our DOM rendering to the client, we will no longer spend server time constructing partial views and sending them to the customer. This will lower the consistent web traffic and allow us to slowly shift our monolith to become a REST service.

* **Build with “shiny” battle hardened tools similar across OnShift**
    React.JS is an industry accepted UI microframework that is heavily used and supported by a large community. OnShift has newer offerings built using this technology and we want to leverage all of tech findings in our legacy application without engaging in a major frontend rewrite.

# Let's Do It

We're excited to dive into the approach we took in our refactoring efforts, and we hope you might be able to benefit from them, too. Just keep in mind that refactoring a legacy application can be a long process. Make sure you address what you want out of a refactor effort before committing to a new pattern.

Now, let's get started in [Refactoring a Legacy Application with React - Part 1]({% post_url 2017-08-07-Refactoring-with-reactJS-components-inside-out %})