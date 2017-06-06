<img src='https://gliechtenstein.github.io/images/llogo.png' class='logo'>

<div class='header'>
<a href="https://www.celljs.org" class="btn btn-primary">Home</a>
<a href="https://github.com/intercellular/cell" class="btn btn-secondary">Github</a>
<a href="https://play.celljs.org" class="btn btn-secondary">Demo</a>
<a href="https://twitter.com/_celljs" class="btn btn-secondary">Twitter</a>
</div>

# Introduction

Cell is an API-less framework that creates a **self-contained & self-driving DOM**.

- **No API** : Remember that Cell is all about how you structure your logic and NOT about learning how to use some proprietary API. This document will walk you through this.
- **Self contained** : Each element is its own universe (context) with its own variables and functions.
- **Self driving** : Each element communicates with outside world via stateless function calls. This means each cell can survive on its own and plug into anything with zero overhead/footprint.

<br>

# How to use this document

Because of the unique traits mentioned above, learning Cell is mostly about understanding how Cell works, and not about how to use and memorize some API methods, **because there is no API**.

**In fact, you don't even need to read this tutorial if you just understand [the 3 rules](https://www.celljs.org/#there-are-only-3-rules)**.

Here's my suggestion:

1. First go ahead and head over to the homepage where it explains [the 3 rules](https://www.celljs.org/#there-are-only-3-rules).
2. Head over to the [demo page](https://play.celljs.org) and play around with it.
3. Come back to this document to learn more advanced topics.

<br>

# Concepts

Building a dynamic web app with Cell can be summarized into the following 3 steps:

> [1. How to create HTML elements from scratch.](#1-how-to-create-html-elements-from-scratch)
>
> [2. How to store data and run programs on elements.](#2-how-to-store-data-and-run-programs-on-elements)
>
> [3. How to make elements communicate with one another.](#3-inter-cellular-communication)

<br>

# 1. How to create HTML elements from scratch

Cell creates HTML elements (Cell calls a created element a "Phenotype") from a blueprint (called "Genotype") written in JSON. "Blueprint" sounds like some serious piece of technology but it's nothing more than just an ordinary javascript variable that describes how each HTML element should look and behave.

All you need to do is declare a variable following a simple naming convention. And then Cell takes care of the rest to turn it into an actual HTML element.

Let's start wit 6 special attributes (The only keywords you need to remember when using Cell, ever) you can use to define an element's structure.

Keyword      |  Explanation
-------------|------------------------------------
`$cell`		| A marker to indicate that this is a Cell element and not just a normal Javascript object
`$type`      | Dom Element type (Example: `div`, `span`, `textarea`, etc.)
`$text`      | Text content inside the element
`$components`| Array of child Genotypes
`$init`      | The function that runs automatically after the element is created
`$update`    | The function that runs automatically whenever some data stored on the element updates.

<br>

First, let's take a look at `$cell`, `$type` and `$text`.

<br>

## a. Creating a simple element

When defining a Genotype variable, it really doesn't matter what you name it. To demonstrate this, we'll name it `ಠᴥಠ`.

```js
ಠᴥಠ = {
	$cell: true,
	$type: "span",
	$text: "Hello"
}
```

Creates:

```html
<span>Hello</span>
```

- `$cell: true` indicates that this object desribes an HTML element. (If excluded, it will be treated as just an ordinary object and nothing will render)
- `$type` describes the type of a node--in this case `span`.
- `$text` describes the text content of a node--in this case `Hello`.

As we already mentioned, the variable name doesn't matter. The only thing that matters is that you include the `$cell` key, and Cell will turn that variable into an HTML element.

<br>

## b. Creating nested elements

This time let's take a look at `$components`.

`$components` is an array that contains genotypes of child elements. We will take the `span` we created above and nest it inside a `div` element.

```js
ಠᴥಠ = {
	$cell: true,
	$type: "div",
	$components: [
		{ $type: "span", $text: "Hello" },
		{ $type: "span", $text: "World" }
	]
}
```

Creates:

```html
<div>
	<span>Hello</span>
	<span>World</span>
</div>
```

<br>

## c. Setting DOM attributes

DOM attributes are first class citizens in Cell. 

This means that by default all the attributes will map 1:1 to DOM element attributes (unless you're using the 6 special "$"-prefixed keywords from above). Here's an example:

```js
$el = {
	$type: "div",
	class: "container",
	$components: [{
		$type: "a",
		$text: "Google",
		href: "https://www.google.com"
	}]
}
```

Becomes:

```html
<div class='container'>
  <a href="https://www.google.com">Google</a>
</div>
```

<br>

## d. Setting event handlers

Setting DOM properties is not limited to string attributes, you can even attach native DOM event handlers. Here's an example:

```js
ಠᴥಠ = {
  $cell: true,
  $type: "button",
  $text: "Button",
  onclick: function(e){
    alert("clicked!");
  }
}
```

creates a button like this:

```html
<button>Button</button>
```

and attach the `onclick` event handler, so it will display an alert `"clicked!"` when you click.


<br>

# 2. How to store data and run programs on elements

## Concepts

##### [A. Application Context](#a-application-context)
##### [B. Containerizing Data and Functions](#b-containerizing-data-and-functions)
##### [C. Creating Self-driving Cells](#c-creating-self-driving-cells)

<br>

## A. Application Context

Every element created with Cell is completely self-contained and can survive on its own. In fact, the only thing Cell (the library) does is create this "smart DOM" at the beginning and then goes away. There's no need for a central structure to maintain all the bindings and event handling.

This is possible because each element functions as its own app execution context, by storing its own **data** and **functions** under its own context.

Let's look at an example:

```js
ಠᴥಠ = {
  $cell: true,
  $type: "input",
  class: "textfield",
  onchange: function(e){
    alert("The value is " + this.value + " and the class name is " + this.class);
  }
}
```

This will turn into a textfield that looks like:

```html
<input class="textfield">
```

The important part here is what's going on inside the `onchange` handler.

Notice we're using `this.value` to access the textfield's native [value](https://www.w3schools.com/tags/att_input_value.asp) attribute, and `this.class` to access the [class](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/class) attribute we just defined right above.

##### TLDR: Cell binds all the variable and function's context to the element they're defined on, so you can directly manipulate them by using the `this` context.

<br>

## B. Containerizing Data and Functions

Now for the most important part: We know how to express the looks, but how do we store data and application directly on the HTML element?

We've seen from above that:

1. to define cell's structure we use the [6 reserved attributes](#1-how-to-create-html-elements-from-scratch)
2. to define native DOM attributes, we [just define them directly](#c-setting-dom-attributes) (with no prefix).

Now here's the third and the last part to this rule:

> To define a data on an element, you use the `_` prefix.

Here's an example:


```js
ಠᴥಠ = {
  $type: "button",
  $text: "Click me",
  _index: 0,
  _items: ["Coffee", "Tea", "Sparkling Water", "Soft Drink"]
}
```

We have successfully defined `_index` and `_items` on the `button` element. This is all! There is no special method to call in order to store data. You simply define it directly, just like you would define an ordinary attribute. Just remember to prefix your variable with `_`.

So how do we use it?

Let's try shuffling through this `_items` array by incrementing the `_index` attribute whenever you click the button. Just remember, all attributes are bound to the element's context, so you can use the `this` context to access them.

```js
ಠᴥಠ = {
  $type: "button",
  $text: "Click me",
  _index: 0,
  _items: ["Coffee", "Tea", "Sparkling Water", "Soft Drink"],
  onclick: function(){
    this._index++;
    this.$text = this._items[this._index];
  }
}
```

## C. Creating Self-Driving Cells

So far we have looked at how each element can be self-contained by storing data and functions on its own context.

But just storing all those data and logic won't do anything on its own. To make each element truly **autonomous** (Start its own lifecycle and keep running on its own), we need ways to describe how each cell:

1. Kickstarts its lifecycle (`$init`)
2. Reacts to internal and external changes to sustain itself. (`$update`)

<br>

### 1. $init

The `$init` function, when defined, gets automatically executed right after the element gets created.

This is useful if you need to apply some dynamic logic on your element at initialization.

For example you can use `$init` to apply some 3rd party library, such as **jQuery, Select2, Bootstrap, CodeMirror, etc.**

Let's first take a static example **without** `$init`.

```javascript
var Model = [ {value: "AL", caption: "Alabama"}, {value: "WY", caption: "Wyoming"} ]
var Option = function(m){
  return { $type: "option", value: m.value, $text: m.caption }
}
var selector = {
  $cell: true,
  $type: "select",
  class: "js-example-basic-single",
  $components: Model.map(Option)
}
```

This will create the following HTML:


```html
<select class="js-example-basic-single">
  <option value="AL">Alabama</option>
  <option value="WY">Wyoming</option>
</select>
```

Now let's use `$init` to turn this into a `select2` component. Just include an `$init` function that looks like this:

```javascript
$init: function(){
  $(this).select2()
}
```

Here's the full code:

```javascript
var Model = [ {value: "AL", caption: "Alabama"}, {value: "WY", caption: "Wyoming"} ]
var Option = function(m){
  return { $type: "option", value: m.value, $text: m.caption }
}
var selector2 = {
  $type: "select",
  class: "js-example-basic-single",
  $init: function(){
    $(this).select2()   // $init function turns this into Select2!
  },
  $components: Model.map(Option)
}
```

<br>

### 2. $update

The `$update` function, when defined, gets executed automatically when any data stored on the element gets updated.


To explain this concept, let's try building an app that:

1. Fetches a remote API (Github API)
2. Store the data under an element when we get the API response.
3. Let that "storing" event automatically trigger `$update`, where we render it.


```js
var GithubApp = {
  $cell: true,
  _items: [],
  $init: function(){
    this._github();
  },
  _github: function(){
    fetch("https://api.github.com/search/repositories?q=javascript").then(function(res){
      return res.json();
    }).then(function(res){
      this._refresh(res.items);
    }.bind(this)); 
  },
  _refresh: function(result){
    this._items = result;
  },
  _template: function(item){
    return {
      $components: [{
        $text: item.full_name
      }, {
        content: item.description ? item.description : ""
      }]
    };
  },
  $update: function(){
    this.$components = this._items.map(this._template);
  }
}
```

Let's walk through the code step by step.

1. `$cell: true` : We declare the `$cell` attribute to declare that this is a cell object. Cell will turn this into an HTML element.
2. `_items: []` : All variables MUST be declared on the object initially if you want to track them. For example, if you want `$update()` to auto trigger whenever `_items` updates, you need to declare the `_items` key on the object, like we did here.
3. `$init()` : When this element gets created, this function will auto-execute. What we want to do is fetch the Github API when this element gets created, so we call `this._github()` here.
1. `_github()` : This function's job is to fetch from the Github API. We use the browser's [fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) API to make a request to Github Trending search API to search for "javascript" ([https://api.github.com/search/repositories?q=javascript](https://api.github.com/search/repositories?q=javascript")) When we get the return value, we turn it into JSON (`return res.json()`), and then we call `this._refresh()`.
2. `_refresh()` : This function simply takes the result from the `_github()` function and stores it on the `_items` array.
3. `$update()` : **Here's the good stuff happens.** `$update()` is triggered automatically when `_items` gets updated, because we declared `_items` on the object at the beginning. We want to update `$components` with what we got from Github, so we run a map on `this.__items` with `this._template` and our Github app is updated.

<br>

## D. Summary

Here's the summary of all the attribute types:

Starts with                    | Explanation                                    | Example
--------------------------|------------------------------------------------|----------------
"$"			                | **DOM construction metadata** Special properties used to construct the node. Updating them directly modifies the DOM. | `$cell`, `$type`, `$text`, `$components`, `$init`, `$update`
"_"				             | **Custom Variables** Declare variables local to your node and use from your app logic. Modifying them doesn't have direct effect on the DOM| `_model`, `_request`, etc. Name them whatever you want as long as they start with "_".
Default 				      | **Everything else**, by default gets mapped 1:1 to DOM Attributes. Updating them directly modifies the DOM. | `style`, `class`, `href`, `onclick`, `onkeyup`, etc.

<br>


# 3. Inter-Cellular Communication

So far we know how to create elements and how to bring them to life by storing data and running app logic dierctly on them.

Now let's take a look at how to let these cells communicate with one another to create a truly decentralized DOM.

Normally this would be the most confusing part in web app frameworks, but for Cell this is the easiest part, because all we need to do to let cells communicate is:

1. Select an element (Simply using native HTML selectors!)
2. Call its function

Let's look at how we can get a handle on an element. 


## A. HTML Selectors

**Cell creates vanilla HTML elements.** This means we can use the browser's own native API, such as `getElementById`, `querySelector`, `querySelectorAll`, etc.

Here's an example:

```js
ಠᴥಠ = {
  $components: [{
    $type: "div",
    id: "console",
    $text: "",
    _refresh: function(text){
      this.$text = text;
    }
  }, {
    $type: "input",
    onkeyup: function(e){
      if(e.keyCode === 13) {
        document.querySelector("#console")._refresh(this.value);
      }
    }
  }]
}
```

Notice how we're simply using `document.querySelector("#console")` to select the first component, and then calling its `_refresh` function. Simple as that. So whatever you type into the input created by Cell will appear in the #console created by Cell.

## B. Context Inheritance and Polymorphism

Earlier we discussed how all variables and functions are bound to their container element's context, allowing us to refer to them simply through `this`.

This is the default behavior. But there's a second behavior I haven't mentioned yet:

**The `this` context gets inherited down the DOM tree.**

Let me explain what it means.

<br>

### 1. Quick Object Oriented Programming Analogy
 
Here's an analogy using a typical object oriented programming example:

```java
public class Animal
{
  public int age;
  public string gender;
}
public class Human extends Animal
{
  public string firstName;
  public string lastName;
}

var tom = new Human();
tom.age = 1;
tom.gender = "male";
```

Here we take an `Animal` class, and extend it to create a subclass `Human`. Because it inherits its parent attributes, we can simply create a `Human` instance and access the attributes inherited from the `Animal` class (`age` and `gender`).

<br>

### 2. How Cell implements "context inheritance"

Well, Cell doesn't have any of these classes and objects. Instead you operate by calling stateless functions. So how does Cell implement this type of behavior?

Cell has a concept called "context inheritance". The underlying premise is the same as class inheritance but the implementation is different, and is **designed specifically for HTML DOM**. Let's look at an example:


```
{
  $cell: true,
  $type: "div",
  id: "container",
  _items: [],
  $components: [
    {
      $type: "input",
      onkeyup: function(e){
        if(e.keyCode === 13){
          this._items.push(this.value);
        }
      }
    }  		
  ]
}
```

Notice how we have `_items` declared on the root element (`div#container`), but we are able to access it from the `onkeyup()` handler of one of its descendants (the `input` element).

Basically whenever you refer to an attribute, the element first looks for the variable on itself. If it doesn't find one, it traverses up the DOM tree until it finds the closest ancestor that has that attribute.

This way you can store a model at any level on the DOM tree and retrieve & manipulate it from any of its descendants. 

So for example, we can implement the `$update()` function on the `div#container` and handle all synchronization there, like this:


```
{
  $cell: true,
  $type: "div",
  id: "container",
  _items: [],
  $update: function(){
    this.querySelector(".list")._refresh();
  },
  $components: [
    {
      $type: "input",
      onkeyup: function(e){
        if(e.keyCode === 13){
          this._items.push(this.value);
          this.value = "";
        }
      }
    },
    {
      class: "list",
      _refresh: function(){
        this.$components = this._items.map(function(item){
          return { class: "row", $text: item };
        })
      }
    }  		
  ]
}
```

Here's what's going on:

1. `onkeyup()` inside the `input` element: It checks to see if the keyCode is 13 (enter key), and when the user presses enter, it tries to push its value into `this._items` array (`this._items.push(this.value)`). Cell searches for the `_items` attribute on the `input`, but it can't find one so it traverses the DOM tree upwards until it finds one at `div#container` (the root element).
2. Once the `_items` array is found, we push the value into it. This triggers an `$update()` function on the `div#container` because one of its data variables has changed.
3. The `$update()` function searches for one of its descendants with the selector `.list`, and calls `_refresh()` function on it (`this.querySelector(".list")._refresh()`).
4. The `div.list` component has a `_refresh` function which takes `this._items` array and creates a `$components` array from it. Notice how the same inheritance principle applies here. `this._items` is not directly defined on the `div.list` element, so it searches upward the DOM tree until it finds one, and uses that value, which happens to be the same `this._items` we set earlier. (We could have just passed around the array as the function's argument, but this was for the sake of explanation).

## C. Pass Element as callback argument

Sometimes you may want to:

1. Call Element `#callee`'s function `_doSomething()` from Element `#caller`.
2. When function `_doSomething()` finishes it somehow returns the control back to the element `#caller`

Of course we could do it like this:

```js
ಠᴥಠ = {
  $components: [{
    id: "caller",
    onclick: function(e){
      document.querySelector("#callee")._doSomething();
    },
    _done: function(response){
      console.log(response);
    }
  }, {
    id: "callee",
    _doSomething: function(){
      // ... do something here...
      document.querySelector("#caller")._done(response);
    }
  }]
}
```

What's going on:

1. When you click on the `#caller` element, it selects the `#callee` element and calls `_doSomething()` function on it
2. When `_doSomething()` finishes it selects the originating element and calls its `_done()` function to return the value.


This is perfectily feasible but another way to do this is by passing the **caller** element itself as an argument, so that we have the handle when we're ready to call its `_done()` function and we don't have to run the `querySelector` command one more time. Here's the same example, using this approach:

```js
ಠᴥಠ = {
  $components: [{
    id: "caller",
    onclick: function(e){
      document.querySelector("#callee")._doSomething(this);
    },
    _done: function(response){
      console.log(response);
    }
  }, {
    id: "callee",
    _doSomething: function($caller){
      // ... do something here...
      $caller._done(response);
    }
  }]
}
```

Notice that we're passing `this` as an argument to `_doSomething()`, so that `#callee` now has the handle back to the `#caller` element.

<br>

