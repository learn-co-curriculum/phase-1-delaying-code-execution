# Delaying Script Execution

## Learning Goals

- Explain why delaying execution of JavaScript code is important
- Use the `defer` attribute to delay execution
- Use the `DOMContentLoaded` event to delay execution
- Control timing of code execution using the placement of the script

## Introduction

One of the things JavaScript enables us to do is to provide dynamic content in
web pages when the page loads. The page's HTML basically provides a framework
within which the content will be populated by JavaScript code. In order for this
to work, however, the HTML "framework" (the DOM) must be fully loaded so that
the script can access the elements and update their content. Trying to
manipulate the DOM before the page fully loads can lead to problems.

In this lesson, we'll go over some options for controlling the timing of code
execution to ensure our code runs correctly.

## Using the `defer` Attribute

One way of controlling when our code executes is to use the `defer` attribute of
the HTML `script` tag.

Say we have a script tag in our `index.html` file that looks like this:

```html
<script type="text/javascript" src="index.js"></script>
```

Keeping this script from executing until after the DOM is fully loaded is a
simple matter of adding the `defer` attribute:

```html
<script defer type="text/javascript" src="index.js"></script>
```

That's it!

### Seeing `defer` in Action

Take a look at the `index.html` file. You will see that there are two `script`
tags in the file. The first lists `index.js` as the source file. The second
contains an inline script.

Now go ahead and open `index.html` in the browser, then open the DevTools. You
should see the following in the console:

```console
This console.log() is in the first script tag
This console.log() is in the second script tag
```

Each `console.log` is fired when the corresponding line is parsed in the HTML,
so they are logged in the order in which they appear in the file.

Now add the `defer` attribute to the first script tag:

```html
<script defer type="text/javascript" src="index.js"></script>
```

Save the file and refresh the browser page. You should now see the two logs
reversed. The second log still fires when the line is parsed in `index.html`,
but the execution of the first log is deferred until after the DOM is completely
loaded.

## The JavaScript `DOMContentLoaded` Event

The DOM API includes another option for delaying code execution until after the
DOM is fully loaded: the `DOMContentLoaded` event. Until fairly recently,
`DOMContentLoaded` was the best option available, and its use is a widely
accepted standard. As a result, you are likely to see it in existing code.

The `DOMContentLoaded` event is the browser's built-in way to indicate when a
page's HTML has finished loading into the DOM. It is set up the same way as any
other event. We call `addEventListener`, passing in the name of the event and a
callback function:

```js
document.addEventListener("DOMContentLoaded", function() {
  // JavaScript code we want to run once the DOM is loaded
});
```

As with any event listener, when the event is "heard", i.e., when the DOM has
fully loaded, the callback function is executed. The callback contains all the
JavaScript code we want to run once the DOM finishes loading.

### Seeing `DOMContentLoaded` in Action

Go back to the `index.html` file and make the following two changes:

- Remove the `defer` attribute from the first script.
- Remove (or comment out) the second script.

Next, delete the `console.log()` that's currently in `index.js` and replace it
with the following:

```js
document.addEventListener("DOMContentLoaded", function() {
  console.log("This will log after the DOM loads");
});
  
console.log(
  "This console.log() fires when index.js loads - before DOMContentLoaded is triggered"
);
```

<details><summary><b>What do you expect to see when you refresh the page in the browser?</b></summary>
    <ul>
      <li>The second <code>console.log()</code> appears first, and the <code>console.log()</code> inside
the callback function appears second.</li>
    </ul>
</details>

## Using the Placement of the Script Tag

There are two common approaches used for placing scripts within an HTML
document: in the `<head>` element or at the end of the `<body>` element.

Generally speaking, scripts belong more naturally in the `<head>`, since that's
where other resources are included (for example, links to stylesheets). However,
as we saw above, HTML is executed synchronously, from top to bottom, so if we
don't use `defer` or `DOMContentLoaded`, the script will be fetched and executed
as soon as the parser reaches it. While this is happening, parsing of the HTML
pauses, causing the page to load more slowly. Futhermore, as we learned above,
if the script includes DOM manipulation, it may not execute properly.

Because of the way the HTML is parsed, moving scripts to the bottom of the
`<body>` element is another way to solve the problem of code executing too soon.
With the script at the bottom, parsing of the HTML completes, **then** the
script is loaded and executed.

Given this, why do we need `defer` or `DOMContentLoaded`?

First, as mentioned above, it allows us to keep our scripts in the head, which
some people prefer.

Second, when the script is included in the `<head>`, there is a performance
boost: with either `defer` or `DOMContentLoaded`, the script is _loaded_ at the
same time the HTML is being parsed, but _script execution_ is deferred until
after the DOM is fully loaded. Because the script is already available when the
DOM finishes loading, execution begins immediately and doesn't have to wait for
the script to be loaded.

It's important to note, however, that the improved performance gained from
including the script in the `<head>` is likely to be undetectable in most
circumstances. As a result, some people advocate for simply listing scripts at
the end of the `<body>` element to keep things as simple as possible. See, for
example, [this article][running-code], which also includes a nice explanation of
the topics covered in this lesson.

Regardless of which approach you take, it is important to understand the options
available to you and how these tools function when you see them in existing
code.

## One Final Point

Note that **none** of the options we've discussed for delaying code execution
waits for CSS stylesheets or images to load. It is most often the case that we
only need the HTML content to fully load in order to execute our JavaScript. In
situations where you need _everything_ to completely load, you will need to use
the [`load`][load] event instead. Just keep in mind that, since images can take
some time to load, it may make a noticeable difference in how long it takes the
page to render fully.

The setup for the `load` event is exactly the same as for `DOMContentLoaded`,
except that it is called on `Window` rather than `document`.

## Conclusion

JavaScript provides us the powerful ability to update webpage content without
refreshing. We can, for instance, have a page with some basic HTML structure and
use JavaScript to fill in the content, enabling the possibility of dynamic
webpages.

This sort of action, however, will only work if the HTML content is loaded on
the page before the JavaScript is executed. Either the `defer` attribute on the
`script` tag or the `DOMContentLoaded` event will ensure that our JavaScript
code is executed as soon as the HTML is finished loading.

## Resources

- [defer][]
- [DOMContentLoaded](https://developer.mozilla.org/en-US/docs/Web/Events/DOMContentLoaded)

[defer]: https://www.w3schools.com/tags/att_script_defer.asp
[defer-visualization]: https://html.spec.whatwg.org/images/asyncdefer.svg
[running-code]: https://www.kirupa.com/html5/running_your_code_at_the_right_time.htm
[load]: https://developer.mozilla.org/en-US/docs/Web/API/Window/load_event
