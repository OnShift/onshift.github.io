---
layout: post
author: ilyaGotfryd
date: 2017-08-07 13:30:00 -0500
title: Refactoring with React.js Components Inside Out
---
Are you working through wads fo unruly JavaScript that accumulated in your web app over the years? Would you like to bring in a concise and testable framework into its place. A common complication a developer experiences when looking to on-board a new frontend framework is how difficult it is to do so incrementally. In this post I would like to describe the journey our team had undergone to make that a reality in our Python backed monolith project.

# What we are going to cover
In the following several posts we will document the following journey:
* Component and it's presentation in HMTL
* Using a bit of jQuery to glue DOM and React.JS components together
* Developer setup necessary to accomplish this
* Testing the components
* Ways to trade state with jQuery and plain JavaScript

# What are the benifits we are looking for
### Individual components mixed in with existing HTML
We want an ability to grow our refactoring incrementally. This way we can narrow our React changes down to individual stories. This allows us to fulfill the requirements of Product Owners and other stakeholders while progressivly removing tech debt and improving clarity.
### Front end testability and common implementation patterns
Using React.JS framework allows us to create code that would be fully tested and testable with abundance of HOW-TO documentation out of the gate.
### Leverage CDN to preload components and render @client
As we are moving our DOM rendering to the client we will no longer spend server time constructing partial views and sending them to the customer. This will lower the consistent web traffic and allow us to slowly shift our monolith to become a REST service.
### Build with “shiny” battle hardened tools similar across OnShift
React.JS is a well industry accepted UI microframework that is heavily used and supported by a large community. We have newer offerings built using this technology and we want to leverage all of tech findings in our legacy without engaging in a major frontend rewrite.

# The Component
## DOM presentation
We want to have an easy way to present component in our HTML. We chose data tag “data-react-component” to identify all of our components.
```
<div data-react-component="FailureBanner" data-message="This is an excellent functional control." data-is-shown="false"> </div>
```
Remainder of the data tags will be pulled into each component as properties with lower camel case names.
Component itself is going to be a pure React.js component that can be usable in any pure React.js project without any code modification.
Here is an example of a component _FailureBanner.jsx_:
 ```
import React from 'react';
import { render } from 'react-dom';

export default class FailureBanner extends React.Component {

    render(){
        if(this.props.isShown) {
            return (
                <div className="flamingo " data-test-component="failureBanner">
                    <div role="alert" className="alert alert-danger" id="msg-text">
                        <div className="alert-left flex-center">
                            <i className="fa fa-check fa-lg" aria-hidden="true"></i>
                        </div>
                        <p id="msg-message">Stuff here:{this.props.message}</p>
                    </div>
                </div>
            );
        }
        return null;
    }
}
```

# The Glue
jQuery that brings them together
* We need to bind multiple DOM elements to respective components
* We need to pull in properties from the DOM elements into component for rendering

We decided to use a pattern commonly reserved to the root element of a React single page application. We wnated to apply that pattern to every element we would bind a react component to in a light weight manner. That resulted in the following solution:
```
import React from 'react';
import { render } from 'react-dom';
import SuccessBanner from './successBanner.jsx';
import FailureBanner from './failureBanner.jsx';
import $ from 'jquery';
import { Provider } from 'react-redux';

const componentRegistry =
    {
        SuccessBanner,
        FailureBanner
    }

//clever way of doing $(document).ready()
$(()=>{
    var reactComponents = $('[data-react-component]');
    $.each(reactComponents, function(index, obj){
        var props = $(obj).data();
        var componentName = $(obj).data('reactComponent');
        var Component = componentRegistry[componentName];
        render(<Component {...props} />, obj);
    });
});
```

Our entry point script creates a registry of the components. On document ready it:
* iterates through the list of DOM elements with _data-react-component_ attribute
* pulls all of the data elements as props
* finds a corresponding component class by name
* calls render with proper Component and a list of stored proparties

# Next
We discuss the developer [setup and testing ->](http://link)