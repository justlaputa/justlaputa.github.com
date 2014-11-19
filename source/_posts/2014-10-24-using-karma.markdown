---
layout: post
title: "Using Karma"
date: 2014-10-24 10:09:48 +0900
comments: true
categories:
---

Recently I'm trying to use [Karma](https://karma-runner.github.io/) to do front-end javascript unit test. Here is my simple startup of setting up.

Boilerplate project
-------

First, let's make a boilerplate project for doing the test. Make prject structure like this:

```
karma-boilerplate
├── package.json
├── README.md
├── src
│   └── hello.js
└── test
    └── hellpSpec.js
```

`hello.js`:

```
function hello() {
  return 0;
}
```

`helloSpec.js`:

```
describe('hello', function() {
  it('should return 0', function() {
    expect(hello()).toEqual(0);
  });
});
```

Or you can directly get the source code from [Github](https://github.com/justlaputa/karma-boilerplate).

Install packages
-------

Karma is written by google's [AngularJS](https://angularjs.org/) team, and they use [Node.js](http://nodejs.org/), so first we need to have node.js installed. If you don't have it, try to install in your platform: [download](http://nodejs.org/download/).

After that, let's install the packages we need:

```
$ npm install karma --save-dev
$ npm install karma-jasmine karma-phantomjs-launcher karma-chrome-launcher --save-dev
```

`karma` is the test runner, it will load our tests in browsers and run them, while others are all karma plugins.

- karma-jasmine: the unit test assertion library, it enables us to write BDD style test cases
- karma-phantomjs-launcher: it enables karma to launcher the headless webkit browser phantomjs, which is convinient to do quick test
- karma-chrome-launcher: it enables karma to launcher google Chrome to do read browser test


Configuration
------

Karma has an [interactive](https://karma-runner.github.io/0.12/intro/configuration.html) command to help us generate config files:

```
$ ./node_modules/karma/bin/karma init
```

It will ask you some questions and you will be able to choose the answers from command line, after that it will generate a `karma.config.js` file for you.

```
$ ./node_modules/karma/bin/karma init

Which testing framework do you want to use ?
Press tab to list possible options. Enter to move to the next question.
> jasmine

Do you want to use Require.js ?
This will add Require.js plugin.
Press tab to list possible options. Enter to move to the next question.
> no

Do you want to capture any browsers automatically ?
Press tab to list possible options. Enter empty string to move to the next question.
> Chrome
> PhantomJS
>

What is the location of your source and test files ?
You can use glob patterns, eg. "js/*.js" or "test/**/*Spec.js".
Enter empty string to move to the next question.
> src/**/*.js
> test/**/*Spec.js
>

Should any of the files included by the previous patterns be excluded ?
You can use glob patterns, eg. "**/*.swp".
Enter empty string to move to the next question.
>

Do you want Karma to watch all the files and run the tests on change ?
Press tab to list possible options.
> yes


Config file generated at "/home/laputa/workspace/github/karma-boilerplate/karma.config.js".
```

Then it will generate `karma.config.js` file for you, there are two points about the karma config:

#### Files

Karma does not serve all files by default, which means it will only serve the files you specified in the config file. The problems come when you are using some loaders like [RequireJS](http://requirejs.org/) or [Google Closure](https://developers.google.com/closure/). You should specify all you source code in `files` configuration, and prevent them to be included in the html page by default. For example:

_RequireJS_:

```
    files: [
      { pattern: 'src/**/*.js', included: false, served: true },
      'test/**/*Spec.js'
    ]
```

_Closure_:

```
    files: [
      { pattern: 'CLOSURE_LIB_ROOT/**/*.js', included: false, served: true },
      'src/**/*Spec.js'
    ]
```

#### Iframe

By default, Karma will load all script in an iframe, and run the test, but when you are using PhantomJS, phantomjs will expose global variables like `window._phantom`. If we want to detect phantomjs in our test source like:

`testSpec.js`:

```
  if (window._phantom) {
    //Add some function that phantomjs not support
  }
```

It won't happen because the `_phantom` global variable will not be set in the iframe. Now we need to specify Karma to not use iframe:

`karma.config.js`:

```
  client: {
    useIframe: false
  }
```

By this, all test will be run in a new window instead of in an iframe.

Use grunt-karma
-------
If you want to use grunt to start karma, which will be helpful for CI runners, you can try to use [grunt-karma](https://github.com/karma-runner/grunt-karma):

```
$ npm install grunt-karma --save-dev
$ npm install karma-junit-reporter --save-dev //this allows to output junit report for CI like Jenkins
```

and you can write all your configuration in the Gruntfile.js instead of in a separate config.js file, it is also able to define separate options for different environment, like CI and local development. A simple example:

`Gruntfile.js`

```
    karma: {
      options: {
        frameworks: ['jasmine'],
        files: [
          'src/**/*.js',
          'test/**/*.js'
        ]
      },
      continuous: {
        singleRun: true,
        browsers: ['PhantomJS'],
        reporters: 'junit',
        junitReporter: {
          outputFile: 'test/reports/karma-results.xml'
        },
        client: {
          useIframe: false //to avoid phantomjs detect failure
        }
      },
      dev: {
        reporters: 'dots',
        bro1wsers: ['Chrome']
      }
    }
```

when run `grunt karma:continuous`, it will avoid using iframe, launch PhantomJS and output junit report for CI. while run `grunt karma:dev` will launcher Chrome for developers.

For the full project, please take a look at my simple [Github source](https://github.com/justlaputa/karma-boilerplate).
