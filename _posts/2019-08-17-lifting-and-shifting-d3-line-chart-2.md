---
layout: post
title:  "Lifting and Shifting a Simple d3.js Line Chart into A Power BI Custom Visual (Part 2)"
author: daniel
categories: [ Custom Visual SDK, Example, TypeScript ]
tags: [ dataviewmappings, capabilities, categorical, dataroles, d3 ]
image: 'assets/images/linechart-intro-example/post-image.png'
description: "Taking an existing example for d3.js and applying to it Power BI as a custom visual. Part 2 handles the visual elements of our chart."
hidden: false
permalink: examples/d3-line-chart-2
---

Hi there! It's time to pick up where we left off in [part 1](/examples/d3-line-chart-1). if you've not read that one, it's probably worth going back and getting up to speed.

For those who are ready to continue, we're going to cover off the remainder of the work to migrate our [reference line chart that we found online](https://www.d3-graph-gallery.com/graph/line_basic.html).

## Code for Our Example

The GitHub repository has been updated with the additional code to complete the migration of our reference visual into a Power BI custom visual, available in the [part-2](https://github.com/dm-p/powerbi-visuals-example-simple-line-chart/releases/tag/part-2) tag (or [this pull request](https://github.com/dm-p/powerbi-visuals-example-simple-line-chart/pull/1)). If you want to start with the code as at the end of part 1, you can run the following in the root of your project:

```
git checkout part-1
```

Let's get on with the show!

## Adding the Chart Container

In our reference visual, a containing element for the chart is added to the page. This is handled by the following code in the *HTML* section:

```html
<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>
```

In our visual, we'll handle this in the `constructor` so that the element is created when instantiated. 

First, we'll ensure that `d3` is imported into our visual code. Add the following to the bottom of the `import` statements in `visual.ts`:

```js
import * as d3 from 'd3';
```

<p class="text-center">
    <a href="https://github.com/dm-p/powerbi-visuals-example-simple-line-chart/commit/800861ae6e52db47b8babca798803f616ec052fa" target="_blank">
        view the changes in Git here
    </a>
</p>

> Note that with the newer SDK it's possible to import `d3` modules individually. We're trying to ensure that our code runs with minimal intervention so we're pulling in the whole library for now. Tweaking the code to use [`d3-selection`](https://github.com/d3/d3/blob/master/API.md#selections-d3-selection) might be a fun little exercise for you :)

Next, we'll add a class property underneath `settings`:

```js
private container: d3.Selection<HTMLDivElement, any, HTMLDivElement, any>;
```

<p class="text-center">
    <a href="https://github.com/dm-p/powerbi-visuals-example-simple-line-chart/commit/4081254f93a4dbbd3118a30ede5137210d9353c3" target="_blank">
        view the changes in Git here
    </a>
</p>

This essentially specifies that our element is an HTML `div` with a parent of `div` also.

In the `constructor` function, we'll now use d3 to add the element to the main container. The function should now look as follows:

```js
constructor(options: VisualConstructorOptions) {
    console.log('Visual constructor', options);
    this.target = options.element;
    this.container = d3.select(this.target)
        .append('div')
            .attr('id', 'my_dataviz');
}
```

<p class="text-center">
    <a href="https://github.com/dm-p/powerbi-visuals-example-simple-line-chart/commit/20ee243e1ff5dbcff5e1b9fb91a22f7328c4e4d7" target="_blank">
        view the changes in Git here
    </a>
</p>

If we refresh the visual and we inspect it using our browser's development tools, then we now see the following element in there:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/linechart-intro-example/div-element-for-chart.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">HTML <code>div</code> element in our visual's DOM with the same <code>id</code> as our reference chart</figcaption>
    </figure>
</div>

Now we have somewhere to attach our chart, we'll move onto the drawing of the chart.

## Dimensions and Margins

The reference code starts with the following code:

```js
// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 60},
    width = 460 - margin.left - margin.right,
    height = 400 - margin.top - margin.bottom;
```

This defines margins and then sets the usable width and height of the chart based on a container size of **460 x 460** pixels. Because our container might be of variable size, we can use the `options.viewport` object in our visual's `update` method to dynamically size our container.

We'll add the equivalent code at the bottom of our update `method`. This is a little different to our reference visual - this is a preference of mine, to ensure that we successfully have data before we do anything else in terms of drawing the visual.

Copy/paste or add the below code into your function, underneath the `console.log(data)` statement:

```js    
    var margin = {top: 10, right: 30, bottom: 30, left: 60},
        width = options.viewport.width - margin.left - margin.right,
        height = options.viewport.height - margin.top - margin.bottom;
```

<p class="text-center">
    <a href="https://github.com/dm-p/powerbi-visuals-example-simple-line-chart/commit/5bca6cd13a2e66b921dcf1692f75d4bc22e698e3" target="_blank">
        view the changes in Git here
    </a>
</p>

## Adding the SVG

Moving through our reference chart, we're now at this bit of the code:

``` js
// append the svg object to the body of the page
var svg = d3.select("#my_dataviz")
  .append("svg")
    .attr("width", width + margin.left + margin.right)
    .attr("height", height + margin.top + margin.bottom)
  .append("g")
    .attr("transform",
          "translate(" + margin.left + "," + margin.top + ")");
```

This is almost possible to copy as typed, but we just need to make sure that we append to the element we created in the constructor. Here's our version - add this underneath the previously-added section of code:

``` js
/** Append the svg object to the body of the page */
    var svg = this.container
        .append("svg")
            .attr("width", width + margin.left + margin.right)
            .attr("height", height + margin.top + margin.bottom)
        .append("g")
            .attr("transform",
                "translate(" + margin.left + "," + margin.top + ")");
```

<p class="text-center">
    <a href="https://github.com/dm-p/powerbi-visuals-example-simple-line-chart/commit/96f776e3090c4ad77e2b5ab9eab378477151d196" target="_blank">
        view the changes in Git here
    </a>
</p>

As you can see, all we've changed is the binding to `this.container` rather than selecting the element by `id`. (my tab indentation is a little different to the above for personal preference).

If we refresh our visual, we see that there is now a `SVG` element (with a `g` element as a child) in the custom visual DOM, with the dimensions of our viewport (less our margins), e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/linechart-intro-example/dom-svg-element-with-sizing.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">HTML <code>svg</code> and <code>g</code> elements in our visual's DOM</figcaption>
    </figure>
</div>

So far, so good. This will also ensure that our `svg` 'canvas' will stick with the visual as we resize it. Let's give that a try and have another look:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/linechart-intro-example/dom-svg-element-with-sizing-superfluous.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">I might need an adult...</figcaption>
    </figure>
</div>

Yikes! That's the wrong size! And there's loads of `svg` elements! Why!? Let's break down what Power BI is doing with the visual:

1. The visual is added and the `constructor` event runs. This adds the `div` element to the canvas (`this.container` in our code).
2. When any change happens in the report page - that can be a resize, data change, cross filter or other interaction, the `update` event in our visual is fired.
3. The `update` method adds a child `svg` element to the `div` we added in our `constructor`.

With our reference chart, we don't need to think about dynamic sizing or other things our user might be doing. It's pretty static.

We need to add some kind of management in our `update` function to manage this. The simplest way is to clear the canvas before we draw our other elements. Add the following to the top of the `update` function, underneath the `this.settings` assignment:

``` js
this.container.selectAll('*').remove();
```

<p class="text-center">
    <a href="https://github.com/dm-p/powerbi-visuals-example-simple-line-chart/commit/e33fe770a6e60c68234503bc1d09e06a8216ef84" target="_blank">
        view the changes in Git here
    </a>
</p>

This will simply clear all elements that are in the container each time `update` runs.

> **Note** that this is an acceptable approach for simple visuals, but if your custom visual potentially contains a lot of complex SVG logic to plot things, then you may want to be more selective in doing this. For now, we're just focusing on the task at hand :)

## The Rest of the Chart

We're now left with the actual code that draws the axes and line. Here's what's left from our reference chart example:

``` js
    // Add X axis --> it is a date format
    var x = d3.scaleTime()
      .domain(d3.extent(data, function(d) { return d.date; }))
      .range([ 0, width ]);
    svg.append("g")
      .attr("transform", "translate(0," + height + ")")
      .call(d3.axisBottom(x));

    // Add Y axis
    var y = d3.scaleLinear()
      .domain([0, d3.max(data, function(d) { return +d.value; })])
      .range([ height, 0 ]);
    svg.append("g")
      .call(d3.axisLeft(y));

    // Add the line
    svg.append("path")
      .datum(data)
      .attr("fill", "none")
      .attr("stroke", "steelblue")
      .attr("stroke-width", 1.5)
      .attr("d", d3.line()
        .x(function(d) { return x(d.date) })
        .y(function(d) { return y(d.value) })
        )
```

We can copy/paste this to the bottom of our `update` function as-is. We just have one teeny-tiny problem to worry about:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/linechart-intro-example/vscode-line-type-error.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Type error on the <code>line</code> function</figcaption>
    </figure>
</div>

To break this down here, TypeScript expects a certain data type in the `line` function. This is assumed to be an array of number pairs (x/y values). In the `datum` function call higher-up, we pass in the `data` variable we declared earlier. This is of type `ILineChartRow`, which is the interface we defined in part 1:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/linechart-intro-example/vscode-line-type-datum.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption"><code>datum</code> function with <code>data</code> variable definition</figcaption>
    </figure>
</div>

This interface tells TypeScript what the expected 'shape' of the datum for each x/y pair is a `Date`/`number` rather than the default. We need to ensure that the `d3.line` function expects this also, so modify this part of the code as follows:

``` js
d3.line<ILineChartRow>()
```

If you're not sure, <a href="https://github.com/dm-p/powerbi-visuals-example-simple-line-chart/commit/558c31402ad9d23f5ff3e2c1ae919fbb2c359e6c" target="_blank">refer to the corresponding Git commit</a> for the solution. I have added a comment at the appropriate location.

Save and our visual should compile as normal. If we refresh, we should see our chart:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/linechart-intro-example/finished-chart.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">You're probably quite used to seeing this by now, but hopefully you're seeing something similar in your own development environment :)</figcaption>
    </figure>
</div>

## Wrapping-Up

So, we've finally got there! Today, we've covered the following:

* Created the main container for our chart in the visual `constructor`.
* Ensuring that `d3` is loaded in our visual.
* Moving the dimension/sizing code across and ensuring it fits our visual viewport.
* Moving the SVG canvas code across and tailoring it to fit our container element.
* Making sure that our `update` function clears down any previously drawn elements when things change.
* Moving the code that plots the axes and the line, and making it TypeScript-compliant.
* Patting ourselves on the back for a job well done!

This was a straightforward example but hopefully gives you some insights into the custom visuals framework and how you might be able to migrate something cool you've seen or previously developed in d3 for use in Power BI.

Now we have our line chart, we can start using it to explore other features that Power BI can offer. If anyone has any suggestions for what they might like to see next as an expansion of this project then please [tweet me](https://twitter.com/the_d_mp), or add a comment below and we'll see what we can do to unpack things a bit.

Thanks very much for reading and I hope that you've found the exercise useful. See you soon!

DM-P