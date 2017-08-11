---
layout: post
authors:
    - ilyaGotfryd
    - aliOrlando
date: 2017-08-10 13:30:00 -0500
title: Shared State&#58; Refactoring a Legacy Application with React - Part 2
---

When we started on this whole project of finding a pattern to refactor in React, we knew we wanted to keep the effort small. Everyone was on the same page: let’s get React in and get it rendering something. If you’ve read Part 1, then you know that we got there. We have React components rendered into the page. Yay! Problem solved!

As you probably know, solving a problem can have a way of creating more problems that you hadn’t thought about before. So what hadn’t we thought about before?

State management.

State management is something that React components do well on their own. Each component has its own state and lifecycle. When React components get to control all of their interactions and state by themselves, this works really well. You might not need any other state management solution in such a case.

In our case, we needed to have control over state from outside of React. With years of built up jQuery code, there’s a lot happening on our legacy front end and we needed the ability to update our React components in response to actions happening elsewhere on the page.

# So what's a dev to do?

One solution I’ve seen before is to unmount the React component, then re-render the component into the DOM with the updated props. It’s a workable solution, but I’ve always disliked it because it completely ignores one of the biggest benefits of using React: the [Virutal DOM](https://www.codecademy.com/articles/react-virtual-dom).

Instead, we took a look around and decided that we needed an application state management solution. In our greenfield React project we use Redux to manage application state. Since we already had this knowledge on the team, we decided to use Redux as well. You could probably just as easily use a reactive library like RxJS. We’ll have to leave that exercise for another day.

# Using the Redux State in React

<!-- Write about connecting the React components to the Redux store (same pattern as Engage) -->

# Interacting with State in Legacy JavaScript

<!-- Write about using the global Redux store to dispatch actions from our legacy code -->

