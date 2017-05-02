---
layout: post
title:  "SASS Fundamentals "
date:   2017-04-13 13:22:43 -0400
tags: 
  - CSS
  - Frontend
  - Trainings
description: 
image: http://yesayasoftware.com/wp-content/uploads/2017/01/sass-logo.jpg
---


I'm in Minneapolis this week fo attend a SASS Fundamentals training by Michael North, hosted by [FrontEndMasters](http://frontendmasters.com).

FrontEndMasters is training company, offering 80+ video courses from experts like **creator of AngularJS, jQuery team members and the founder of JSON.** 

I've been a subscriber for over a year, and Douglas Crockford's course: [The Good Parts of JavaScript and the Web](https://frontendmasters.com/courses/good-parts-javascript-web/) has been a great resource. 

As my skills level up, I've explored other frameworks like [React.js with Brian Holt)](https://frontendmasters.com/courses/complete-intro-react/) and [Angular.js with Lukas Ruebbelke](https://frontendmasters.com/courses/elm/).

As frontend engineers, I'm sure you have experienced frustration with CSS3 and its many quirks.  Ever tried to float an element without a clear fix?  Or coming back to edit your CSS styles after a few weeks?

![](http://a.memegen.com/JzOjFs.gif)

This is where a CSS preprocessor like SASS comes in handy.

## Why use preprocessors?

![](http://www.growingwiththeweb.com/images/2014/03/17/preprocessors.png)

* Easy to [setup tools](http://sass-lang.com/install)
* Easier to maintain and organize style
* Provide extra functionality like variables, nesting and functions
* Write D.R.Y code

Let's dive into SASS!

## 1. Nesting 

Mirroring HTML's clearly nested visual hierachy, SASS enables you to write CSS with nested CSS selectors. Organizing styles with nesting helps to make your code more readable.

#### EXAMPLE: Styles can be nested with indentation

```
.btn {
  padding: 2px 10px;
  border: 1px solid #c46;
  border-radius: 2px;

  &.btn-primary {
    color: white;
    background-color: #cc4466;
  }
}
```


**SASS compiles to CSS, processed from top to bottom
**
```
.btn {
  padding: 2px 10px;
  border: 1px solid #c46;
  border-radius: 2px;
}

.btn.btn-primary {
  color: white;
  background-color: #cc4466;
}
```

## 2. Importing Partials 

In CSS, you can use `@import` to include several stylesheets in your main styles.css file.  But each` @import `results in a new round-trip HTTP request, which affects performance as your app grows.

In SASS, you can create **Partials**. 

A Partial is simply a Sass file named with a leading underscore, e.g. `_reset.css` .  Partials will not be generated into a CSS file, instead includes CSS modules that you can include in the main ` app.css `file.

#### EXAMPLE of a** _reset.scss** partial file imported into **app.scss** from [SASS guide](http://sass-lang.com/guide)

```
// _reset.scss

html,
body,
ul,
ol {
   margin: 0;
  padding: 0;
}

// app.scss

@import 'reset';

body {
  font: 100% Helvetica, sans-serif;
  background-color: #efefef;
}
```

**SASS compiles to CSS** 

```
html, body, ul, ol {
  margin: 0;
  padding: 0;
}

body {
  font: 100% Helvetica, sans-serif;
  background-color: #efefef;
}
```


When compiling, SASS will take the partials file that you want to import and combine it with the file you're importing into so you can **serve a single CSS file** to the web browser! This reduces the number of files users need to download when they land on your page.

You can also store **Variables** in your partial, which can be handy for Brand Style guidelines.  

Always name your variables with the `$` symbol. 

#### EXAMPLE: [Medium's Brand Style](https://www.behance.net/gallery/7226653/Medium-Brand-Development) color palette

![](https://mir-s3-cdn-cf.behance.net/project_modules/disp/68ba2055538977.56098d9c32eaa.jpg)

```
// _variables.scss

$medium-green: rgb(0,185,107);
$medium-white: rgb(252,252,252);
$medium-dark: rgb(42,44,43);


// app.scss

@import 'variables';

.button{
    color: $medium-green;
    background-color: $medium-white;
    text-shadow: 0 0 2px $medium-dark;
}
```

**SASS compiles to CSS** 

```
.button {
  color: #00b96b;
  background-color: #fcfcfc;
  text-shadow: 0 0 2px #2a2c2b;
}
```

When importing, consider if you want the variables to be available GLOBALLY or LOCALLY. In this example, you will likely use brand styles throughout your app - place `@import` on top of file for global access. 

If you only want styles to apply to certain elements, such as `sidebar` , you can place `@import` within the declaration block, e.g.

```
// app.scss

.sidebar{
  @import 'variables';
	
  a {
    color: $medium-green;
    padding: 2px 5px;
  }
}

```

**SASS compiles to CSS **

```
.sidebar a {
  color: #00b96b;
  padding: 2px 5px;
}

```

## 3. Mixins

A `@mixin` lets you make groups of CSS declarations that you want to reuse throughout your app, a practical example is for vendor prefix.

#### EXAMPLE: Create a Mixin for border-width, using a variable $width 

```

// _mixins.scss

@mixin border-width($width) {
  -webkit-border-width: $width;
     -moz-border-width: $width;
      -ms-border-width: $width;
          border-width: $width;
}

// app.scss

.column-1 { @include border-width(5px); }
```

** SASS compiles to CSS **

```
.column-1 {
  -webkit-border-width: 5px;
  -moz-border-width: 5px;
  -ms-border-width: 5px;
  border-width: 5px;
}

```

As you can see, `@mixin` code snippets will be merged into `.column-1`. 

However, if you apply `@mixin` to multipl selectors, you could be repeating your code which increase the size of your CSS file (and is no longer D.R.Y)!  Be aware of this issue and how you use `@mixin`s!


```
// app.scss

.column-1 { @include border-width(5px); }
.column-2 { @include border-width(5px); }
.column-3 { @include border-width(5px); }
```

** SASS compiles to CSS **

```
.column-1 {
  -webkit-border-width: 5px;
  -moz-border-width: 5px;
  -ms-border-width: 5px;
  border-width: 5px;
}

.column-2 {
  -webkit-border-width: 5px;
  -moz-border-width: 5px;
  -ms-border-width: 5px;
  border-width: 5px;
}

.column-3 {
  -webkit-border-width: 5px;
  -moz-border-width: 5px;
  -ms-border-width: 5px;
  border-width: 5px;
}

```

## 4. Extending Placeholders 




