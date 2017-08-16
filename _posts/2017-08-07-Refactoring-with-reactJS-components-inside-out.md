---
layout: post
authors:
    - ilyaGotfryd
    - aliOrlando
date: 2017-08-08 13:30:00 -0500
title: Components Inside Out&#58; Refactoring a Legacy Application with React - Part 1
excerpt: In this post, we're going to jump in to the approach that we've taken here at OnShift to start refactoring our legacy front-end using React.
edits:
  - date: 2017-08-14 17:00:00 -0500
    description: Moved the series introduction to its own post, updated the flow of the document, improved code highlighting
---
*Note: This post is part of a series, starting with [Refactoring a Legacy Application with React - Introduction]({% post_url 2017-08-14-Refactoring-with-reactJS-introduction %})*

In this post, we're going to jump in to the approach that we've taken here at OnShift to start refactoring our legacy front-end using React. Previously, this post included some background that we've chosen to move to its own introductory post [Refactoring a Legacy Application with React - Introduction]({% post_url 2017-08-14-Refactoring-with-reactJS-introduction %}). If you feel a bit lost, you should start there.

# Refactoring Inside Out

We mentioned in the Introduction that being able to refactor in small, incremental steps is important to the success of this project. We'll have small UI changes driving the creation of React components in our application. We'll be able to focus on small parts of the UI and grow out from there until an entire page is written using React. The first step we need to take is to write a small, contained React component.

## A Simple Component

Let's start with a simple React component, `FailureBanner` that we want to display in our jQuery legacy application. The component displays an alert with an icon and a message when when its `isShown` prop is truthy.

{% highlight javascript linenos %}
import React from 'react';

export default class FailureBanner extends React.Component {
    render(){
        if(this.props.isShown) {
            // displays an alert with given message property when isShown is true
            return (
                <div className="flamingo">
                    <div role="alert" className="alert alert-danger" id="msg-text">
                        <div className="alert-left flex-center">
                            <i className="fa fa-ban fa-lg" aria-hidden="true"></i>
                        </div>
                        <p>{this.props.message}</p>
                    </div>
                </div>
            );
        }
        // when hidden, render nothing
        return null;
    }
}
{% endhighlight %}

## DOM presentation

Now that we have a simple React component, we need to know where to render it. The existing legacy application we have to work with renders HTML views on the server and sends to the client.

We wanted to have an easy way to present components in our HTML, so we chose to use a data attribute `data-react-component` to identify an attachment point for a component.
{% highlight html linenos %}
<div data-react-component="FailureBanner"> </div>
{% endhighlight %}
By using jQuery selectors, we'll be able to find any tags that include the `data-react-component` attribute and render the given component into the DOM - in this case, the `FailureBanner` component.

We've already seen that our `FailureBanner` component is expecting some props in order to determine how it renders. How can we provide these props to the component on render?

We decided to leverage [data attributes](https://developer.mozilla.org/en-US/docs/Learn/HTML/Howto/Use_data_attributes) again to provide any props to the component from the HTML tag.

{% highlight html linenos %}
<div data-react-component="FailureBanner" data-message="This is an excellent functional control." data-is-shown="false"> </div>
{% endhighlight %}
Using jQuery's `.data()` function, we can transform the data attributes into camel case props `message` and `isShown`. We'll use the `data-message` and `data-is-shown` attributes as the component's props in just a moment.

## The Glue: Initial Rendering of Multiple Components into the DOM

So we have a React component and we have an HTML tag with some data attributes that are related to the component. Now we actually need to render the component into the appropriate DOM tags. Since we want to start our refactor efforts small, we need a way to render multiple small React components into the DOM. For example, we might want to have 3 separate `FailureBanner` components on the same page and render them with the appropriate props.

We decided to use a pattern commonly reserved to the root element of a React single page application. We wanted to apply that pattern to every element we would bind a react component to in a light weight manner. That resulted in the following solution:

{% highlight javascript linenos %}
import React from 'react';
import { render } from 'react-dom';
import SuccessBanner from './successBanner.jsx';
import FailureBanner from './failureBanner.jsx';
import $ from 'jquery';

// Build a registry of components with the component name as the key
const componentRegistry = {
    SuccessBanner,
    FailureBanner
};

// clever way of doing $(document).ready()
$(()=>{
    // Find all the `data-react-component` tags in the DOM
    const reactComponents = $('[data-react-component]');

    $.each(reactComponents, function(index, obj) {
        // Retrieve the data attributes to use as component props
        const props = $(obj).data();

        // Get the component name of the `data-react-component` attribute
        const componentName = $(obj).data('reactComponent');

        // Find the component in the registry.
        // React keeps no such registry and cannot render a component just from a string name.
        const Component = componentRegistry[componentName];

        if (Component) {
            // Use React DOM library's render to render the component into the tag (as long as it was found in the registry)
            render(<Component {...props} />, obj);
        }
    });
});
{% endhighlight %}

Each line includes a comment to explain the process, but let's summarize:
1. Our entry point script creates a registry of the components. It's important to keep this registry because React must have the component class/function in order to render the component. It doesn't keep a registry of components as strings.
2. On document ready it:
  * Iterates through the list of DOM elements with `data-react-component` attribute
  * Pulls all of the data attributes as props for the component
  * Finds a corresponding component class by name from the registry
  * Calls render with proper Component and a list of stored properties

This piece accomplishes the initial rendering of React components into our DOM with the appropriate props.

# Next:
[Shared State: Refactoring Legacy Applications with React - Part 2]({% post_url 2017-08-15-Refactoring-with-reactJS-shared-state %})

Now that we have React components rendered into our pages, we need to explore how we update the components in response to actions elsewhere on the page. [Join us in Part 2]({% post_url 2017-08-15-Refactoring-with-reactJS-shared-state %}) where we'll talk about our solution for shared state management between React and legacy JavaScript code.
