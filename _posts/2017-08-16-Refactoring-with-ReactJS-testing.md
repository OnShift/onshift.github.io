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
   tool_description: The main goal for Karma is to bring a productive testing environment to developers. The environment being one where they don't have to set up loads of configurations, but rather a place where developers can just write the code and get instant feedback from their tests.
   tool_url: https&#58;//karma-runner.github.io/1.0/index.html
 - image_path: /images/mocha_icon.png
   tool_name: Mocha
   tool_description: Feature-rich JavaScript test framework running on Node.js
   tool_url: https&#58;//mochajs.org/
 - image_path: /images/chai_icon.png
   tool_name: Chai
   tool_description: BDD / TDD assertion library for node and the browser that can be delightfully paired with any javascript testing framework.
   tool_url: http&#58;//chaijs.com/
 - image_path: /images/sinon_icon.png
   tool_name: Sinon
   tool_description: Standalone test spies, stubs and mocks for JavaScript. Works with any unit testing framework.
   tool_url: http&#58;//sinonjs.org/
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
        </div><div class="col-md-11 vcenter">
        <b>{{ tool.tool_name }}</b> - {{ tool.tool_description }}
        </div></div>
    {% endfor %}
</div>

To give you a bit of guidance our file structure has been presently chosen as follows:
```json
\static
    ├──reactjs-components
    |   ├──js
    |   |   └──components.js      <- transpiled output file 
    |   ├──jsx
    |   |   ├──actions            <- actions folder
    |   |   ├──reducers           <- reducers folder
    |   |   ├──index.jsx          <- base entry point for the package
    |   |   └──successBanner.jsx  <- sample react JS component
    |   ├──test
    |   |   ├──mocha
    |   |   |   └──sepc
    |   |   |       └──sendMessageTest.spec.js  <- Mocha test file
    |   |   ├──setup.jsx
    |   |   └──test_helper.jsx    <- custom built test helper code
    |   └──karma.conf.js          <- carma configuration
    └── package.json              <- npm package configuration
```

## Test Utility Configuration
We started all the way at the top with NPM adding a command to execute our karma tests in the scripts section of the _package.json_ file:
```json
{ "scripts":
    {
      "test-react-component": "karma start reactjs_components/karma.conf.js"
    }
}
```

That leads us to our karma file configuration:
```json
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
```

The rest of the Karma configuration is standard base layout using PhantomJS for DOM container.