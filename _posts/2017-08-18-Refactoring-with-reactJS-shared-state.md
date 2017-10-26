---
layout: post
authors:
    - aliOrlando
    - ilyaGotfryd
date: 2017-08-22 13:30:00 -0500
title: "Shared State: Refactoring a Legacy Application with React - Part 2"
excerpt: "When we started on this whole project of finding a pattern to refactor in React, we knew we wanted to keep the effort small. Everyone was on the same page: let’s get React in and get it rendering something."
---
*Note: This post is part of a series, starting with [Refactoring a Legacy Application with React - Introduction]({% post_url 2017-08-14-Refactoring-with-reactJS-introduction %})*

When we started on this whole project of finding a pattern to refactor in React, we knew we wanted to keep the effort small. Everyone was on the same page: let’s get React in and get it rendering something. If you’ve read Part 1, then you know that we got there. We have React components rendered into the page. Yay! Problem solved!

As you probably know, solving a problem can have a way of creating more problems that you hadn’t thought about before. So what hadn’t we thought about before?

State management.

Ok... what the heck does that mean?

State management refers to how we are managing the data in our UI over time. For example, the state of the application tells us whether or not to display a certain piece of UI, which item a user has currently selected, or the current value of a text input on the page.

Our legacy code relies almost entirely on direct DOM manipulation to achieve dynamic behavior. Click a button? We're listening for that button click, receiving the event, and doing all of our event handling and DOM manipulation in one handler. Should it update 20 places in the UI? We'll select all 20 places in the UI and update them in the handler. The DOM reflects the current state of the application and any JavaScript code we write handles whatever cases it should.

Handling UI updates by manipulating 20 separate pieces of the DOM is _expensive_ and extremely _inefficient_. This is where a state management solution comes in.

[Local state management](https://facebook.github.io/react/docs/state-and-lifecycle.html) is something that React provides as a feature out of the box. Each component has its own encapsulated state and lifecycle allowing it to define its own behavior. When React components control all of their interactions and state by themselves, this works really well. You might not need any other state management solution in such a case.

In our case, we needed to have control over state from outside of React. With years of built up jQuery code, there’s a lot happening on our legacy front end and we need the ability to update our React components in response to actions happening elsewhere on the page.

# The Problem

Let's look at the `FailureBanner` component from last time.

### Original FailureBanner.jsx
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

Using our `FailureBanner` example from last time, let's suppose that we want to display the failure banner to a user only after they've clicked a button on the page. Let's assume that the button is rendered by the server and just exists on the existing page.

{% highlight html linenos %}
<script type="text/javascript">
    $('#failureButton').click(function(e) {
        // LEGACY CODE - does some crazy stuff to the rest of the page

        // Here is where we want to display our React failure banner with a specific message
    });
</script>

<div data-react-component='FailureBanner' data-is-shown='false' data-message=''></div>
<button id='failureButton' >Do The Thing</button>

<!-- Include the react initialization code from last time
It renders the FailureBanner on page load with the isShown and message props set to the values in the tag -->
<script src='index.js'></script>
{% endhighlight %}

In this example, we already have an instance of the `FailureBanner` rendered into the page with some default properties. In this case, it's hidden from the user because we set the isShown prop to `false`. Once the user clicks our legacy failure button, the application does a bunch of stuff. At the very end, we've decided that it should now show a failure banner to the user. We want to update the `FailureBanner` component's props so that `isShown` is true and the `message` says "Sorry, we couldn't do the thing."

# How do we update component props?

One solution we’ve seen before is to unmount the React component, then re-render the component into the DOM with the updated props. It’s a workable solution, but it ignores one of the biggest benefits of using React: avoiding DOM manipulation via the [Virtual DOM](https://www.codecademy.com/articles/react-virtual-dom).

Trying to keep our proof of concept to a minimum, we attempted to capture components at the time of creation and inject some form of function or object to give us global access to individual component's state. After several attempts with marginal degree of success we realized that we were creating a sufficiently complex home grown system that attempts to replicate existing technologies. For the sake of brevity and community support, we decided to turn to an industry accepted state management solution already used elsewhere in-house. In our greenfield React project we use [Redux](http://redux.js.org/) to manage application state. Since we already had this knowledge on the team, we decided to use Redux in our legacy refactoring as well. One could probably just as easily use a reactive library like [RxJS](http://reactivex.io/rxjs/). We’ll have to leave that as an exercise for another day.

Our Redux store will be the source of truth for the application's state. Our React components will be connected to the store and can dispatch changes to the store and/or react to application state changes appropriately. Similarly, our legacy Javascript will be able to access the store and dispatch actions to change application state.

# Create the Redux Store

First, we need to create a Redux store for our application. We're going to keep this piece with the React initilization script from part 1. Since the store needs to be accessible from all of our legacy code we decided to take the meaning of "global store" literally and attach it to the window object. Please keep your 'boos' to a light rumble and stick with us. We promise it gets better.

The new code here is line `11`, where we create the store using our reducer (which we'll cover next) and lines `29`-`34` where we wrap each component we render in `react-redux`'s `Provider` component to give it access to the store.

{% highlight javascript linenos %}
import React from 'react';
import ReactDOM from 'react-dom';
import SuccessBanner from './successBanner.jsx';
import FailureBanner from './failureBanner.jsx';
import $ from 'jquery';
import { createStore } from 'redux';
import { Provider } from 'react-redux';
import reducer from './reducers/index.jsx'

// create the Redux store on the window to make accessible to whole application
window.store = createStore(reducer);

const componentRegistry = {
    SuccessBanner,
    FailureBanner
};

$(()=>{
    const reactComponents = $('[data-react-component]');

    $.each(reactComponents, function(index, obj) {
        const props = $(obj).data();
        const componentName = $(obj).data('reactComponent');
        const Component = componentRegistry[componentName];

        if (Component) {
            // Render the component into the DOM
            // wrapped in Provider to give it access to the Redux store
            ReactDOM.render(
                <Provider store={window.store}>
                    <Component {...props} />
                </Provider>,
                obj
            );
        }
    });
});
{% endhighlight %}

Since we're interested in keeping track of the `FailureBanner`'s props in the application state, we wrote a simple reducer (that the store is created with). In this example, we assume that we only need to track the props for one `FailureBanner`. We store the `isShown` and `message` props as the application state. Any action dispatched with the type `SET_FAILURE_BANNER` updates the properties on the state.

{% highlight javascript linenos %}
const initialState = {
    isShown: false,
    message: ''
};
export default function(state = initialState, action) {
    switch (action.type) {
        case 'SET_FAILURE_BANNER':
            return {
                isShown: action.payload.isShown,
                message: action.payload.message
            };
        default:
            return state;
    }
}
{% endhighlight %}

Finally, we'll write an [action creator](https://daveceddia.com/redux-action-creators/) that creates the action object we want to dispatch when we want to change the Failure Banner state. This pattern allows for consistent readable code and ensures fewer typo-based failures, among other things.

{% highlight javascript linenos %}
function setFailureBanner(isShown = false, message = '') {
    return {
        type: 'SET_FAILURE_BANNER',
        payload: {
            isShown,
            message
        }
    };
}
{% endhighlight %}

Now that we have a basic Redux store setup, we need to use the application state in our components.

# Using the Redux State in React

We'll address the most common pattern - using Redux with React. There are a lot of excellent resources that cover how to add Redux to your React application, including [this basic tutorial in the Redux docs](http://redux.js.org/docs/basics/). We trust that you're either familiar with the pattern or can read about it elsewhere, so we won't go into too much depth on the specifics. We made an effort to provide lightest seams possible in our implementation in order to preserve common uses of React and Redux. This means that all of the common implementation patterns and documentation do apply.

We'll be using the [react-redux library](https://github.com/reactjs/react-redux) to make binding React components to the Redux store simple. The library provides the `connect` [higher order component](https://facebook.github.io/react/docs/higher-order-components.html) that we use to wrap our components and map them to the application state they care about.

Let's look at how we'll change the `FailureBanner` component that we saw earlier. The main difference is the newly exported default which wraps the `FailureBanner` in the `connect` function. The `mapStateToProps` function receives the `state` (the current state of the Redux store) as a parameter and is responsible for mapping the pieces of state we care about to the component, which will receive the keys as props.

{% highlight javascript linenos %}
import React from 'react';
import { connect } from 'react-redux';

export class FailureBanner extends React.Component {
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

function mapStateToProps(state) {
    return {
        isShown: state.isShown,
        message: state.message
    };
}

export default connect(mapStateToProps)(FailureBanner);
{% endhighlight %}

Any time the Redux store updates the `isShown` and `message` state, `connect` will handle the change and rerender our `FailureBanner` component with the new props.

# Interacting with State in Legacy JavaScript

At this point, we have a `FailureBanner` component that is updating based on a global application state. Any time the application state changes, it rerenders with the new state. This makes updating our already initialized React components easy - all we have to do is tell the Redux store to update and our components automatically react to that.

To update the Redux store, we can dispatch actions that are handled by our reducers. Since the store is created on the window, we can dispatch our `setFailureBanner` action like so:

{% highlight javascript %}
// assume that we have the setFailureBanner action creator from earlier in scope here
window.store.dispatch(setFailureBanner(true, "Sorry, we couldn't do the thing :( "));
{% endhighlight %}

Our problem from earlier, where we want to display the FailureBanner when a button is clicked becomes:

{% highlight html linenos %}
<script type="text/javascript">
    $('#failureButton').click(function(e) {
        // LEGACY CODE - does some crazy stuff to the rest of the page

        // Here is where we want to display our React failure banner with a specific message
        window.store.dispatch(setFailureBanner(true, "Sorry, we couldn't do the thing :( "));
    });
</script>

<!-- No initial props are defined here since the Redux store already has the initial props! -->
<div data-react-component='FailureBanner'></div>
<button id='failureButton' >Do The Thing</button>

<!-- Include the react initialization code from last time
It renders the FailureBanner on page load with the isShown and message props set to the values in the tag -->
<script src='index.js'></script>
{% endhighlight %}

We could even make the message disappear after a certain amount of time:

{% highlight javascript linenos %}
// Display the Failure Banner
window.store.dispatch(setFailureBanner(true, "Sorry, we couldn't do the thing :( "));

// hide the Failure Banner after 5 seconds
setTimeout(function() {
    window.store.dispatch(setFailureBanner(false));
}, 5000);
{% endhighlight %}

Now that our legacy code can control reactions in our components, we'll be able to harness the power of the Virtual DOM and stay in sync with the rest of the application!

# Next: Unit Testing Components

[Testing and Infrastructure: Refactoring a Legacy Application in React - Part 3]({% post_url 2017-08-28-Refactoring-with-ReactJS-testing %})

One of the major goals of this refactor effort was creating maintainable and testable code. Find out how we're testing our new React code in the [next part]({% post_url 2017-08-28-Refactoring-with-ReactJS-testing %}) of this series!

