---
layout: post
authors:
    - ilyaGotfryd
    - aliOrlando
date: 2017-08-08 13:30:00 -0500
title: Components Inside Out&#58; Refactoring a Legacy Application with React - Part 1
edits:
  - date: 2017-08-11 17:00:00 -0500
    description: Moved introduction to its own post
---
Are you working through wads of unruly JavaScript that accumulated in your web app over the years? Would you like to bring in a concise and testable framework into its place?

In this post, we're going to jump in to the approach that we've taken here at OnShift to start refactoring our legacy front-end using React. Previously, this post included some background that we've chosen to move to its own introductory post [Refactoring a Legacy Application with React - Introduction]({% post_url 2017-08-11-Refactoring-with-reactJS-introduction %}). If you feel a bit lost, you should start there.

# The Component

## A Simple Component
Let's start with a simple React component, `FailureBanner` that we want to display in our jQuery legacy application. Let's suppose this component displays an alert message and is conditionally shown like so:
 ```javascript
import React from 'react';
import { render } from 'react-dom';

export default class FailureBanner extends React.Component {
    render(){
        if(this.props.isShown) {
            // displays an alert with given message property when isShown is true
            return (
                <div className="flamingo">
                    <div role="alert" className="alert alert-danger" id="msg-text">
                        <div className="alert-left flex-center">
                            <i className="fa fa-check fa-lg" aria-hidden="true"></i>
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
```

## DOM presentation

The existing legacy application we have to work with renders HTML views on the server and sends to the client. Since we want to start our refactor efforts small, we need a way to render multiple small React components into the DOM. For example, we might want to have 3 separate `FailureBanner` components on the same page.

We wanted to have an easy way to present components in our HTML, so we chose to use a data attribute `data-react-component` to identify an attachment point for a component.
```html
<div data-react-component="FailureBanner"> </div>
```
By using jQuery selectors, we'll be able to find any tags that include the `data-react-component` attribute and render the given component into the DOM - in this case, the `FailureBanner` component.

We've already seen that our `FailureBanner` component is expecting some props in order to determine how it renders. How can we provide these props to the component on render?

We decided to leverage [data attributes](https://developer.mozilla.org/en-US/docs/Learn/HTML/Howto/Use_data_attributes) again to provide any props to the component from the HTML tag.

```html
<div data-react-component="FailureBanner" data-message="This is an excellent functional control." data-is-shown="false"> </div>
```
The `data-message` and `data-is-shown` attributes will be pulled into each component as properties with lower camel case names `message` and `isShown`.

# The Glue

jQuery that brings them together
* We need to bind multiple DOM elements to respective components
* We need to pull in properties from the DOM elements into component for rendering

We decided to use a pattern commonly reserved to the root element of a React single page application. We wanted to apply that pattern to every element we would bind a react component to in a light weight manner. That resulted in the following solution:

```javascript
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
```

Each line includes a comment to explain the process, but let's summarize:
1. Our entry point script creates a registry of the components. It's important to keep this registry because React must have the component class/function in order to render the component. It doesn't keep a registry of your components as strings.
2. On document ready it:
  * Iterates through the list of DOM elements with `data-react-component` attribute
  * Pulls all of the data attributes as props for the component
  * Finds a corresponding component class by name from the registry
  * Calls render with proper Component and a list of stored properties

# Coming Soon - Part 2
We discuss the developer [setup and testing](#)