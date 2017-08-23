---
layout: post
authors:
 - ilyaGotfryd
 - aliOrlando
date: 2017-08-16 01:30:00 -0500
title: "Testing and infrastructure: Refactoring a Legacy Application in React - Part 3"
test_tools:
 - image_path: /images/karma_icon.png
   tool_name: Karma
   tool_description: The main goal for Karma is to bring a productive testing environment to developers. The environment being one where they don't have to set up loads of configurations, but rather a place where developers can just write the code and get instant feedback from their tests.
   tool_url: "https://karma-runner.github.io/1.0/index.html"
 - image_path: /images/mocha_icon.png
   tool_name: Mocha
   tool_description: Feature-rich JavaScript test framework running on Node.js
   tool_url: "https://mochajs.org/"
 - image_path: /images/chai_icon.png
   tool_name: Chai
   tool_description: BDD / TDD assertion library for node and the browser that can be delightfully paired with any javascript testing framework.
   tool_url: "http://chaijs.com/"
 - image_path: /images/sinon_icon.png
   tool_name: Sinon
   tool_description: Standalone test spies, stubs and mocks for JavaScript. Works with any unit testing framework.
   tool_url: "http://sinonjs.org/"
---


*Note: This post is part of a series, starting with [Refactoring a Legacy Application with React - Introduction]({% post_url 2017-08-14-Refactoring-with-reactJS-introduction %})*

In this post we are going to cover the essential part that makes move to React as a front-end framework so highly desirable. We are talking of course about testing.

# Topics we cover
  * Tool set with short description of game pieces and choices
  * Test utility configuration
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
    {% for tool in toolz %}
        <div class="row">
        <div class="col-md-1 vcenter">
        <a href="{{tool.tool_url}}"><img class="tool-icon" src="{{ tool.image_path }}"  /></a>
        </div><div class="col-md-10 vcenter">
        <b>{{ tool.tool_name }}</b> - {{ tool.tool_description }}
        </div></div>
    {% endfor %}
</div>
## File structure
To give you a bit of guidance and a point of reference our file structure is presently arranged as follows:
```
\static
    ├──reactjs-components
    |   ├──js
    |   |   └──components.js      <- transpiled output file 
    |   ├──jsx
    |   |   ├──actions            <- actions folder
    |   |   ├──reducers           <- reducers folder
    |   |   ├──index.jsx          <- base entry point for the package
    |   |   └──failureBanner.jsx  <- sample react JS component
    |   ├──test
    |   |   ├──mocha
    |   |   |   └──sepc
    |   |   |       └──failureBanner.spec.js  <- Mocha test file
    |   |   ├──setup.jsx
    |   |   └──test_helper.jsx    <- custom built test helper code
    |   └──karma.conf.js          <- Karma configuration
    └── package.json              <- npm package configuration
```

## Test Utility Configuration
We started all the way at the top with NPM adding a command to execute our karma tests in the scripts section of the _package.json_ file:
{% highlight javascript linenos %}
{ "scripts":
    {
      "test-react-component": "karma start reactjs_components/karma.conf.js"
    }
}
{% endhighlight %}

To configure our Karma test runner we have chosen a common base karma.conf.js file with the following modifications:
{% highlight javascript linenos %}
module.exports = function(config){
  config.set(
  {
    // ...
    files: [                              // files that get loaded into Mocha testing context
      // ...
      'js/components.js',                 // transpiled code that is being tested
      'test/mocha/specs/**/*.spec.js'],   // masked path to all of the test files
    preprocessors: {
      'jsx/**/*.jsx': ['browserify'] ,    // path to transpile components and associated files
      'test/**/*.js': ['browserify']      // path to transpile test files
    },

    browserify: {
      debug: true,
      transform: [
        // when transpiling components ignore tests
        ['babelify', {presets: ['es2015', 'react'], ignore: 'test/**/*.js'}],
        // when transpiling tests exlude components
        ['babelify', {presets: ['es2015'], ignore: 'jsx/**/*.jsx'}]
      ],
    },
    // ...
  });
}
{% endhighlight %}

The rest of the Karma configuration is standard base layout using PhantomJS for DOM container.

# Tests
Before exploring the unique utilities that we have used let's dive into a basic render test. We expect that you either have worked with Mocha/Chai before or would follow the following link for common documentation here: [mocha.js](https://mochajs.org/#getting-started) You will find link to Chai assertion framework a bit deeper into the page of the link included.
{% highlight javascript linenos %}
import { renderComponent, expect } from '../../test_helper.jsx';
import FailureBanner from '../../../jsx/failureBanner.jsx';

describe("failure banner", function(){
    var component;

    beforeEach(function(done){
        component = renderComponent(FailureBanner, {message: 'test this failure message', isShown: true});
        done();
    })

    it("renders failure banner", function(){
        expect(component.$el.data('test-component')).to.equal('failureBanner');
    })
});
{% endhighlight %}

Let's go through the code play-by-play:
{% highlight javascript %}
  import { renderComponent, expect } from '../../test_helper.jsx';
{% endhighlight %}
Chai - the JS test assertion library uses function `expect`. We are importing `expect` from our helper simply because we have a wrapper for a regular Chai functionality that outputs a bit more of information if assertion fails.
`renderComponent` is a generic component rendering wrapper that is suitable for any DOM rendering container. We will discuss it later as we are going through the helper code.

{% highlight javascript %}
  import FailureBanner from '../../../jsx/failureBanner.jsx';
{% endhighlight %}
Importing tested component from its relative path. Here as everywhere you might notice that we are importing components with their JSX extensions. This is a shortcoming in our build configuration that we were not yet able to overcome. Presently if you omit JSX it will get resolved to JS and fail to find the component.
<p />Following `describe` method is comes from Mocha framework to wrap a suit of tests.<p />
Let's look inside `beforeEach` call and dive into a bit more of a detail on rendering the component for testing.
{% highlight javascript linenos %}
beforeEach(function(done){
        component = renderComponent(FailureBanner, {message: 'test this failure message', isShown: true});
        done();
    });
{% endhighlight %}
As we set out to render a component using our `renderComponent` helper method we have to pass in the ReactJS component being renderd as well as an object that reporesents properties we would like for this object to assume prior to rendering. As you can see the object is going to be shown with the included message.
It is important to include `done` parameter into the function call and then to call it. We need to ensure that rendering of the object was completed before any tests have been performed on the rendered object. As you can see done parameter is a function that needs to be called to indicate that processing has been completed and the rest of the test can be executed.
<p />
Now let's take a look back at the component for reference in order to understand the assertion line.
{% highlight javascript linenos %}
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
{% endhighlight %}
At the line `9` you can see `data-test-component="failureBanner"` and when this component is renderd this is the tag that we are latching on in this test.
{% highlight javascript %}
  expect(component.$el.data('test-component')).to.equal('failureBanner');
{% endhighlight %}
Now its time to look at the helper methods that we used here.

# Test Helper Methods

# Conclusion
Hope this gave you enough information to get you started with testing your newly baked ReactJS components. In the future ReactJS testing posts we will discuss testing components as the state changes.
