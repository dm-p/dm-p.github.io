---
layout: post
title: "A Study on Hills and Valleys in Power BI with Deneb"
author: daniel
categories: [Design, Prototyping]
tags: [deneb, vega, vega-lite, line-chart]
description: "These are very useful plots, but they come with some tricky requirements to get the shaded areas exactly right..."
image: "assets/images/deneb/hill-valley/finished-recipe.png"
featured: false
toc: false
permalink: deneb_hill_valley
---

> It's hard to find the exact name for these types of charts, but <a href="https://twitter.com/InsightsMachine" target="_blank">Jeff Weir</a> often refers to them as "hill and valley", and I'm quite taken with that. Either way, they're a type of line/area chart that uses conditionally shaded areas to highlight variance (typically actual to target). They're seen a lot in visuals that adhere to <a href="https://www.ibcs.com/" target="_blank">IBCS</a>. If anyone wishes to provide me with some guidance on the "right" way to name them, I'm very happy to amend the post accordingly :)
>
> I've been meaning to write this one up for a long time, mainly because upon working it out with Deneb earlier this year, <a href="https://powerbi.microsoft.com/en-us/blog/power-bi-march-2022-feature-summary/#post-18792-_Toc98245362:~:text=before%20general%20availability.-,Error%20bars,-You%20can%20now" target="_blank">the error bar functionality for core visuals was introduced as a public preview</a> came out, and then I tried focus on solving it natively.
>
> **[DISCLAIMER]** this is a walkthrough, but it's a lot of work. I'm honestly not sure if there's an audience for this type of content, but I'll let you decide as to whether it floats your boat. It's was certainly interesting challenge for me and I've learned some neat tricks that might be handy for other folks learning to "think visually". The resulting specification and template could benefit from a lot more work, and this article focuses specifically on the aspect of getting the areas and lines right.

---

## Introduction

Error bars in Power BI have been a really powerful feature for core visuals, and as usual, folks in the community are finding incredibly creative uses for them. Here's a couple of my recent favourites:

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Let me show you a Unicorn. Line Chart visual. Calculation Group. <a href="https://t.co/qfqVelKbnR">pic.twitter.com/qfqVelKbnR</a></p>&mdash; Andrzej Leszkiewicz 🇺🇦🇵🇱 (@avatorl) <a href="https://twitter.com/avatorl/status/1560038101245886465?ref_src=twsrc%5Etfw">August 17, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Sharing how to do some tricks with Error Bars to get &quot;target areas&quot; in your Power BI line charts.<br><br>Have you tried this <a href="https://twitter.com/hashtag/powerbi?src=hash&amp;ref_src=twsrc%5Etfw">#powerbi</a> trick before?<br><br>Full tutorial on my latest blog post 👇👇👇<a href="https://t.co/7wWE3CMQkj">https://t.co/7wWE3CMQkj</a> <a href="https://t.co/KDzWtjKRY0">pic.twitter.com/KDzWtjKRY0</a></p>&mdash; Mara Pereira (@datapears) <a href="https://twitter.com/datapears/status/1552991189875425280?ref_src=twsrc%5Etfw">July 29, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

---

Another person who's been doing a lot of work to expose their potential is <a href="https://twitter.com/eSven_nl" target="_blank">Sven Boekhoven</a>:

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Difficult to create a shaded area between two lines? Not any more! With the help of Error bars this became really easy. <br><br>Have a look at my quick start guide: <a href="https://t.co/oTolKl23tc">https://t.co/oTolKl23tc</a><a href="https://twitter.com/hashtag/PowerBI?src=hash&amp;ref_src=twsrc%5Etfw">#PowerBI</a> <a href="https://t.co/guHAVtNaVb">pic.twitter.com/guHAVtNaVb</a></p>&mdash; Sven Boekhoven (@eSven_nl) <a href="https://twitter.com/eSven_nl/status/1557396498639380480?ref_src=twsrc%5Etfw">August 10, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

---

When I saw this, I was immediately drawn to the top-right chart, and I excitedly thought Sven had cracked the challenge I've been trying to solve in Power BI for ages:

> "Can we produce a hill and valley chart with core visuals?"

The answer is - as it often is... "it depends".

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">You’re not. That’s the way it is <a href="https://t.co/2AzUMGe8Yk">https://t.co/2AzUMGe8Yk</a></p>&mdash; Jeff &quot;It&#39;s not a feature, it&#39;s a bug&quot; Weir (@InsightsMachine) <a href="https://twitter.com/InsightsMachine/status/1559703689379528704?ref_src=twsrc%5Etfw">August 17, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

---

## What's happening here?

Quite simply, the error bar functionality is doing what it's intended to do: shading the gap between the lines.

However, the lines have their own "slope" (or gradient) between each data point on the x-axis, and these don't always line up with the gradient of the line we would need to only shade the inner area. Let's have a look at this with a more specific example.

We have a regular old line chart, with our [Month] on the x-axis, and measures for [Sales] and [Sales PY] (previous year):

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/problem-core-line-01.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">A simple line chart, showing our [Sales] and [Sales&nbsp;PY] measures by month, plus a table of their raw values.</figcaption>
    </figure>
</div>

If we create a measure to obtain the lowest value of the two measures for each month and apply this to the upper bound of the error band for our [Sales] measure in the analytics pane (refer to <a href="http://www.informationmagician.com/shaded-area-between-two-lines/" target="_blank">Sven's excellent post</a> for a walkthough on this), we can see where things start to go wrong (and I've added the measure as a line to help accentuate the effect further):

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/problem-core-line-02.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">After adding an error band, we can see that there are some shaded areas under the lines, which are not what we want.</figcaption>
    </figure>
</div>

If we overlay the data points for [Sales] as markers, and take a look at the first problem region (October - December), we can zero-in further on the root cause:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/problem-core-line-03.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Our data points "snap" to each month (left diagram), but in order for the error band to intersect in the correct place, we need some additional data points that resolve the correct Date (x-axis) and Sales (y-axis) values, so that the shaded area is correct (right diagram).</figcaption>
    </figure>
</div>

So, in order for our shaded area to be correct, we have to interpolate some data points into our visual's data that exist at the place where our lines intersect. The challenges we face here are numerous:

- The x-axis for a line chart requires a column from the data model in order to create row context.

- Because we're using the month from our date table as the axis value (even if we use a column that uses the month start date for aggregation), the intersection date/time is going to be more finer-grained than this (down to hours, minutes or even seconds if we want to be very precise).

- We may have multiple intersection points for the same chart (what goes up might come down... or back up again..?)

- The above points imply that we have dynamic aspects that we need to cater for, and measures are a better fit for this.

---

## This is a real head-scratcher...

As this point, if I'm to solve this with core visuals, I need to find a way to create row context for all months in my visual's dataset + all derived intersection points that may occur... and that's something that's way above my skill level in that particular neck of the woods 😵‍💫

I did try this first, and I'm sure that someone may be able to do this by using date-level rows and some measures. Sometimes we don't always succeed and I'm going to share what I <i>do</i> know, and how we can solve it with Deneb and Vega-Lite, which allows more more control over our visual dataset via transforms. Even though the row context is the same, we can create the additional rows in Vega-Lite with the right approach.

As usual, I'd encoourage you to read the implementation below, to see where I'm going with the approach (and maybe even find a better way to do it), but if you want the template to start working with, <a href="https://gist.github.com/dm-p/7976dc964437124ecf6830a74d9b2e03" target="_blank">it's available in this Gist</a>.

> A lot of time went into also researching and developing the linear equation work we're going to cover below in DAX, but have not opted to continue, due to the challenges with visualising this data using the core visuals. However, I appreciate that my DAX knowledge is not the best, so someone else may well be able to excel where I have failed... so maybe it won't go to waste.
>
> The DAX approach can also be used in lieu of the top-level Vega-Lite transforms, if you prefer to keep everyting in-model, but doing it in Vega-Lite makes templating easier, as there are many less dependencies to worry about. [I've added this as a postscript after the main article](#postscript-attempting-the-linear-algebra-via-dax), so feel free to read and see if you can make it work. I'd love to know if you do!

---

## Working out the intersection points

At a high-level, I'm going to approach the problem as follows:

- For each data point (month), there may be an intersection point between our two measures that occurs prior to the next data point (following month).

- I can use the current row context and following row context to determine if this occurs.

- Because I'm using a cartesian coordinate system (line chart), I can solve the equations of the gradient produced by the data points between months to work out their intersection coordinates.

- I can then use these coordinates to create additional data points in my visual later on (once I've worked them out).

If we were using Excel, we would have the `SLOPE` and `INTERCEPT` formulas to help us, but we don't have these in DAX, so we're going to do it all the way from the bottom-up.

Fortunately, the math in this space is very well-established, so it's time to get re-acquainted with linear equations. I really wish I believed my teachers when they said I may need them one day...

Taking our example above, and focusing on one data point (October) and its subsequent row (November), we can imagine each of our lines as follows:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/measure-gradient-illustration.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Each segment of a line (from one month to the next) has its own gradient.</figcaption>
    </figure>
</div>

It's really easy to find out how to calculate the intersection between two lines. Its debatable as to whether it's easy to understand, but <a href="https://en.wikipedia.org/wiki/Line%E2%80%93line_intersection" target="_blank">I'll leave the Wikipedia article on it here</a> for those who are interested, and for those who aren't, I'll apply it to the example here.

To summarise this, we can use the equation of a straight line:

> `y = mx + b`

Where **m** is the slope/gradient and **b** is the y-intercept (where x = zero).

As our chart is at a monthly grain, we can derive the current month's measure values and the next month's measure values for each row. These will serve as the reference x/y coordinates for our points of each line, and we will use them to calculate the gradient, and subsequent y-intercept we need for our straight-line equations.

When we have both of these values, we will have an equation for each line in the form _**y = mx + b**_, essentially:

> `[Sales] = ( [Sales Slope] * [Month] ) + [Sales Y-Intercept]`
>
> `[Sales PY] = ( [Sales PY Slope] * [Month] ) + [Sales PY Y-Intercept]`

Because [Month] plays the role of **x** in our equation, we can solve for when both [Sales] and [Sales PY] are equal, i.e.:

> `( [Sales Slope] * [Month] ) + [Sales Y-Intercept] = ( [Sales PY Slope] * [Month] ) + [Sales PY Y-Intercept]`

And, as the [Month] will be equal, we can rearrange this as follows for each coordinate:

> `x = ( [Sales PY Y-Intercept] - [Sales Y-Intercept] ) / ( [Sales Slope] - [Sales PY Slope] )`
>
> `y = [Sales] * ( [Sales PY Y-Intercept] - [Sales Y-Intercept] ) / ( [Sales Slope] - [Sales PY Slope] ) + [Sales Y-Intercept]`

You may have noticed that the x-calculation is common to both of these equations, and we'll take steps to address that also.

## Breaking down the Vega-Lite approach

> As our view _might_ be to template this solution for other measures, we're going to think of the [Sales] measure as our **actual** value, and the [Sales PY] measure as our **target** value in the design concept. So, anything that we're going to consider and establish, calculation-wise will use these terms.

In addition to have to add the linear equation solving to our specification, we need to think about how the layers need to be composed. This one _seems_ simple, but the reality is that you will still need a few layers, drawn in the following order:

1. Target area ("negative"; for when actual measure does not achieve target)
2. Actual area ("positive"; for when actual achieves target)
3. Foreground masking area (including calculated intersection points)
4. Target line
5. Actual line

The first three layers will create the effect that we want, and this should become apparent as we work through the exercise. Whilst Vega-Lite lets us combine a line with the area marks, we may lose some of the lines as the areas are layered, so we will draw these above everything else. Here's how they will look conceptually:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/layer-concepts.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">To implement this approach, we will need 5 layers. The foreground masking layer will actually be the same colour as the background, but has been shaded in this diagram so that you can see where it will sit on the canvas.</figcaption>
    </figure>
</div>

Because the majority of our calculations are needed for layer 3, we'll do the first two layers, so that you can see some progress before we hit the part that is going to slow us down a bit.

---

## Layer 1: the 'target' area

As is typical, the first layer will also set up the axes we need. Firstly I'm going to add my [Month], [Sales] and [Sales PY] fields to my Deneb visual, and create an empty template. I'll then make my changes for layer 1.

###### Config tab

I'm going to start out my visual config in a fairly usual way:

```
{
  "view": {"stroke": "transparent"},
  "font": "Segoe UI",
  "axis": {
    "title": null,
    "grid": false,
    "ticks": false,
    "labelPadding": 10,
    "labelFontSize": 12
  },
  "axisY": {"domain": false},
  "style": {
    "delta_negative": {
      "color": "#8D1D1C"
    }
  }
}
```

This just sets up how I'd like my axes to look, as well as setting the default font and removing the borders around the view. I'm also getting straight into defining `style` entries, as we're using many similar marks, and we want them to have their own distinct styling. The `delta_negative` is using a shade of red that is a bit more colour-blind friendly in conjunction with the green were using, and was inspired from _The Big Picture_ (p.45) by <a href="https://twitter.com/DataRevelations" target="_blank">Steve Wexler</a>.

###### Specification tab

Here' I'll add-in my boilerplate for the encodings and our first layer:

```
{
  "data": {"name": "dataset"},
  "encoding": {
    "x": {
      "field": "Month",
      "type": "temporal"
    },
    "y": {
      "type": "quantitative"
    }
  },
  "layer": [
    {
      "description": "Target area - background",
      "mark": {
        "type": "area",
        "style": "delta_negative"
      },
      "encoding": {
        "y": {"field": "Sales PY"}
      }
    }
  ]
}

```

As we'll be sharing our x and y axes, the `encoding` channel sets up the most common usage for the dependent layers. Our first layer is then added, and our `delta_negative` style is applied. We now have our first part ready to go:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/layer-1.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Our first layer renders our target value ([Sales&nbsp;PY]) as the 'background' area.</figcaption>
    </figure>
</div>

---

## Layer 2: the 'actual' area

The intention for this layer is to track the value of our actual measure ([Sales]), and it is drawn above the target layer on the screen.

###### Config tab

We just need to add-in a `style` entry as follows:

```
{
  ...
  "style": {
    ...
    "delta_positive": {
      "color": "#83C79B"
    }
  }
}
```

###### Specification tab

Our layer configuration is very similar, except for the `stlye` and `y` encoding channel bindings, and is added to the end of the `layer` array:

```
{
  ...
  "layer": [
    ...
    {
      "description": "Actual area - masks out target where necessary",
      "mark": {
        "type": "area",
        "style": "delta_positive"
      },
      "encoding": {
        "y": {"field": "Sales"}
      }
    }
  ]
}
```

Our visual should now look as follows after we apply the changes:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/layer-2.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Our second layer plots our actual value ([Sales]) above the target area (layer 1).</figcaption>
    </figure>
</div>

As we can see, where we didn't hit the target, this still appears from behind the actual layer, but it is masked out when actual exceeds it.

---

## Layer 3: foreground masking area (with intersection points)

We're now onto the tricky bit - we want this layer to be drawn above above the actual layer (2), but contain the intersection points that may exist, in addition to the rows we already have. We'll use transforms for this - and there will be a few - so that we can step through the stages more easily.

#### Initial work

First, the easy parts. We're going to set up our config and add a simple layer in our specification, so that we can build on this as we go.

###### Config tab

We'll add in a `style` entry for our area mark, so that this is ready to go when we finish the changes in the _Specification_ layer:

```
{
  ...
  "style": {
    ...
    "mask_foreground": {
      "color": "#ffffff",
      "stroke": "#ffffff"
    }
  }
}
```

The `color` for this style matches my visual's background colour, so you may need to change this if you're using a different background for your report. We're also adding a `stroke` of the same colour value, so that any of the previous layers don't bleed through to the foreground also.

###### Specification tab

We'll now add a new entry to the `layer` array, with placeholders for the transforms we're going to apply, as well as our mark:

```
{
  ...
  "layer": [
    ...
    {
      "description": "Masking layer (with interpolated points)",
      "transform": [],
      "mark": {
        "type": "area",
        "style": "mask_foreground"
      }
    }
  ]
}
```

We're not adding any `encoding` channels yet, but by doing this, we will be able to inspect the dataset for this layer in the _Data_ pane (which is going to help us a lot).

#### Calculating lowest value

For each month in our dataset, we need to calculate which one of these is the lowest value, as this will influence where we need to apply the masking layer for each month in our dataset. We can do this by adding the following entry to the empty `transform` array we added in the previous step:

```
{
  ...
  "layer": [
    ...
    {
      ...
      "transform": [
        {
          "calculate": "min(datum['Sales PY'] : datum['Sales'])",
          "as": "low_value"
        }
      ],
      ...
    }
  ]
}
```

In the _Data_ pane, we can select the last _data_n_ entry (where _n_ is a number automatically assigned by Vega-Lite) and inspect the results of these calculations, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/layer-3-lowest-calculations.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">The last generated dataset in the <i>Data</i> pane shows the results of our added transforms. We can see that the calculation we have added will resolve the lowest value out of the two measures.</figcaption>
    </figure>
</div>

#### Calculating 'following' fields

In order for us to work out the slope and y-intersect for our line segments, we will need to be able to access the following row in our dataset. We'll add the following group of functions to the end of `transform` array:

```
{
  ...
  "layer": [
    ...
    {
      ...
      "transform": [
        ...
        {
          "window": [
            {
              "op": "lead",
              "field": "Month",
              "as": "month_following"
            },
            {
              "op": "lead",
              "field": "Sales",
              "as": "actual_following"
            },
            {
              "op": "lead",
              "field": "Sales PY",
              "as": "target_following"
            }
          ]
        }
      ],
      ...
    }
  ]
}
```

This is using a window transform, with a lead operation to access the next row, and extract the [Month], [Sales] and [Sales PY] fields (naming them `month_following`, `actual_following` and `target_following`). Here's how they should turn out in the _Data_ pane:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/layer-3-following-calculations.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Our <i>Data</i> pane now shows the results of our added transforms. We have 'copied' the next row's values into the previous row (intervening columns omitted for brevity).</figcaption>
    </figure>
</div>

---

#### Calculating slope values

As we now have our current and following row details, we can calculate the slope of each line segment.

###### Specification tab

We'll now add the following entries to our `transform` array in layer 3:

```
{
  ...
  "layer": [
    ...
    {
      ...
      "transform": [
        ...
        {
          "calculate": "(datum['actual_following'] - datum['Sales']) / (datum['month_following'] - datum['Month'])",
          "as": "actual_slope"
        },
        {
          "calculate": "(datum['target_following'] - datum['Sales PY']) / (datum['month_following'] - datum['Month'])",
          "as": "target_slope"
        }
      ],
      ...
    }
  ]
}
```

These use the slope formulas identified above, and all we're doing is substituting in the values that apply. We'll now get two additional columns in the dataset in the _Data_ pane, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/layer-3-slope-calculations.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">After adding our calculations for each line's slope, we have two additional columns in the <i>Data</i> pane (intervening columns omitted for brevity).</figcaption>
    </figure>
</div>

Even though our x-axis uses date/time, these get converted to numeric representations internally, so can be subtracted from each other just fine 👍

The resulting columns show our slope value, and a positive value represents a "rising" line (moving away from the x-axis), whereas a negative value represents a "falling" line (moving towards the x-axis).

---

#### Calculating y-intersect values

###### Specification tab

Now that we have our slope values, we can calculate the y-intersect values. Similar to above, we add two more entries to the `transform` array:

```
{
  ...
  "layer": [
    ...
    {
      ...
      "transform": [
        ...
        {
          "calculate": "datum['Sales'] - (datum['actual_slope'] * datum['Month'])",
          "as": "actual_y_intercept"
        },
        {
          "calculate": "datum['Sales PY'] - (datum['target_slope'] * datum['Month'])",
          "as": "target_y_intercept"
        }
      ],
      ...
    }
  ]
}
```

As before, we can see the results in the _Data_ pane, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/layer-3-y-intersect-calculations.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">After adding our calculations for each line's y-intersect, we have two additional columns in the <i>Data</i> (intervening columns omitted for brevity).</figcaption>
    </figure>
</div>

These represent the values for each measure where x ([Month]) = 0. These can be quite extreme, relative to our current measure values because the zero for date/time is 1st Jan 1970. The scale doesn't matter though, as long as we know that we have the right components of our straight line equation.

---

#### Calculating intersect points

Now that we have our components, we can calculate the x and y coordinates of where our line segments will intersect.

[As we mentioned above](#working-out-the-intersection-points), the formulas to calculate the coordinates reuse some common logic, so we'll create two calculations we can re-use, and then two for each coordinate. Again, we'll add these to the `transform` array:

```
{
  ...
  "layer": [
    ...
    {
      ...
      "transform": [
        ...
        {
          "calculate": "(datum['target_y_intercept'] - datum['actual_y_intercept']) / (datum['actual_slope'] - datum['target_slope'])",
          "as": "intersect_base"
        },
        {
          "calculate": "datum['intersect_base'] > datum['Month'] && datum['intersect_base'] < datum['month_following']",
          "as": "intersect_before_following"
        },
        {
          "calculate": "datum['intersect_before_following'] ? datetime(datum['intersect_base']) : null",
          "as": "intersect_x"
        },
        {
          "calculate": "datum['intersect_before_following'] ? (datum['actual_slope'] * datum['intersect_base']) + datum['actual_y_intercept'] : null",
          "as": "intersect_y"
        }
      ],
      ...
    }
  ]
}
```

It should be apparent from the sample output below, but here's what we did:

1. `intersect_base`: calculated the x-coordinate where the line segments will intersect. This is resolved to a number representing the timestamp where this will occur.

2. `intersect_before_following`: evaluated whether the intersection point falls between the current and following month as a `true/false` value. We are only interested in the events where this is `true` (we have to plan for an intersection point between months).

3. `intersect_x`: resolved the x-coordinate we wish to utilise from `intersect_base`, if the intersection occurs between rows (`null` if it doesn't). Because this is a number, we wrap this with a `datetime()` function, to convert it back into a valid date/time.

4. `intersect_y`: resolve the y-coordinate for the intersect, based on the x-coordinate (`intersect_base`). Again, we test to see if the intersection occurs between months, and only evaluate the measure value if this is `true` (`null` if it's `false`).

Here's how these should look in the _Data_ pane for our layer's dataset:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/layer-3-intersect-calculations.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">After adding our calculations for the intersection coordinates, we have four additional columns in the <i>Data</i> (intervening columns omitted for brevity).</figcaption>
    </figure>
</div>

Due to the conversion from `intersect_base`, we can see the exact date and time that an intersection would occur, and that this is between the `Month` and the `following_month`. We can make a similar observation with the calculated `y_intersect` value and our measures.

Now we have the values we need to interpolate, we need to create the additional rows.

---

#### Folding the intersects and generating new rows

We had to do a lot to get here, but this is where things start to pay off. We'll add the following to the end of our (very long) `transform` array:

```
{
  ...
  "layer": [
    ...
    {
      ...
      "transform": [
        ...
        {
          "fold": [
            "Month",
            "intersect_x"
          ]
        },
        {"filter": "datum['value'] !== null"}
      ],
      ...
    }
  ]
}
```

<a href="http://vega.github.io/vega-lite/docs/fold.html" target="_blank">The `fold` transform</a> is pretty nifty, and you can think of it much like an unpivot transform in Power Query. We're passing in the two columns that we want to use as our new level of grain, and Vega-Lite will produce new rows of key/value pairs (plus the other fields for the row) to support this structure. We're able to alias the column names, but we're leaving the defaults, and they will have the following structure:

- `key`: the name of the column being folded (either `"Month"` or `"intersect_x"`)
- `value`: the value from the original column (which will be a date/time value).

We apply a `filter` transform to remove the values where the resulting `value` is `null` (no intersection date that falls between current and following month).

This gives us a dataset with a few more rows than before, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/layer-3-fold-calculations.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">After folding our columns and filtering them, we are left with some additional rows to cater for the intersection points as additional columns in the <i>Data</i> (intervening columns omitted for brevity).</figcaption>
    </figure>
</div>

In our dataset, all existing fields are cloned, and we can see there are duplicates for some of the rows, although the values we want (`key` & `value`) are unique. We will need to do one more thing to get the resolved x/y values for our area mark, and this will be the last thing we need to do.

---

#### Resolving the final x/y values for our interpolated area

Our last step on a very long journey for this layer's transforms effectively selects the correct value to use for the x and y encodings that we need for our area. It's been a slog to get here, but hopefully illustrates the scale of the challenge we're facing to get these nice shaded areas...

We will add our last two calculations to the `transform` array:

```
{
  ...
  "layer": [
    ...
    {
      ...
      "transform": [
        ...
          {
            "calculate": "datum['key'] === 'Month' ? datum['Month'] : datum['intersect_x']",
            "as": "x"
          },
          {
            "calculate": "datum['key'] === 'Month' ? datum['lowest_value'] : datum['intersect_y']",
            "as": "y"
          }
      ],
      ...
    }
  ]
}
```

Both of these simply look at the value in our `key` column, and return the correct value to use:

- For `x`, this is the value of the [Month] column if it corresponds with an orginal row from our dataset. If it doesn't we use the calculated intersection value (`intersect_x`).

- For `y` this is the value of the `lowest_value` calculation if it corresponds with an orginal row from our dataset. If it doesn't we use the calculated intersection value (`intersect_y`).

And the final two columns in our dataset is what our work has yielded, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/layer-3-actual-value-calculations.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">After folding our columns and filtering them, we are left with some additional rows to cater for the intersection points as additional columns in the <i>Data</i> (intervening columns omitted for brevity).</figcaption>
    </figure>
</div>

---

#### Updating the layer encodings

###### Specification tab

All(!) we now need to do is update the `encoding` channel for this layer with the calculated values:

```
{
  ...
  "layer": [
    ...
    {
      ...
      "encoding": {
        "x": {"field": "x"},
        "y": {"field": "y"}
      }
    }
  ]
}
```

And, when we apply our changes, we get our hills and valleys 🙂

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/layer-3.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">After binding our folder and resolved x/y values to this layer's x & y encoding channels, our mask now includes the interpolated data points, and this ensures our shaded areas don't have any artefacts.</figcaption>
    </figure>
</div>

We'll now focus on the final two layers, which are (thankfully) much more straightforward.

---

### Layer 4: the target line

Because we're using layers to do our masking, it's better that we draw our lines over the top, so that they're clearly defined.

###### Config tab

Much like for the other marks, we'll add a `style` property for our target line:

```
{
  ...
  "style": {
    ...
    "target_line": {
      "color": "#939393",
      "strokeWidth": 2
    }
  }
}
```

This will colour our line in a mid-tone grey when it's plotted.

###### Specification tab

We can now add a new layer to the end of the top-level `layer` array:

```
{
  ...
  "layer": [
    ...
    {
      "description": "Target line",
      "mark": {
        "type": "line",
        "style": "target_line"
      },
      "encoding": {
        "y": {"field": "Sales PY"}
      }
    }
  ]
}
```

We now get a line that follows the target measure ([Sales PY]):

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/layer-4.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">After adding a line mark to layer four that is encoded for our target ([Sales&nbsp;PY]) measure, we can see how this tracks against the shaded areas.</figcaption>
    </figure>
</div>

---

### Layer 5: the actual line

The last mark we need is our line to show our actual ([Sales]) measure, and this follows the pattern for the previous layer.

###### Config tab

Again, we apply a `style` for our line:

```
{
  ...
  "style": {
    ...
    "actual_line": {
      "color": "#000000",
      "strokeWidth": 3
    }
  }
}
```

The intention here is that we have a more solid black line, and its sligntly thicker than our target line.

###### Specification tab

We add our final mark to the top-level `layer` array:

```
{
  ...
  "layer": [
    ...
    {
      "description": "Actual line",
      "mark": {
        "type": "line",
        "style": "actual_line"
      },
      "encoding": {
        "y": {"field": "Sales"}
      }
    }
  ]
}
```

This only differs to layer 4 in the `style` and `y` encoding bindings. And, upon applying our changes, we get the full effect:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/layer-5.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">After adding a line mark to layer five that is encoded for our actual ([Sales]) measure, we now have a bolder line that indicates our primary values.</figcaption>
    </figure>
</div>

---

### Some final cosmetic tweaks

From this point, we could probably go nuts with improving further, but this article has covered enough ground. The last small change I'm going to make is to apply some additional styling and formatting to my axes by amending the top-level encoding channels to the following:

```
{
  ...
  "encoding": {
    "x": {
      "field": "Month",
      "type": "temporal",
      "axis": {
        "zindex": 1,
        "format": "%b"
      }
    },
    "y": {
      "type": "quantitative",
      "axis": {"tickCount": 5}
    }
  },
  ...
}
```

A short summary of what I've done here is as follows:

- For the x-axis, I've brought it to the top of the canvas, above the marks (`zindex` = 1), so that the masking area is not completely obstructing the domain line. I'm also applying formatting to show the abbreviated month (<a href="https://github.com/d3/d3-time-format/" target="_blank">using D3 syntax</a> rather then the custom Power BI formatter), as in this case I'm always looking at a single financial year.

- For the y-axis, I've fixed the tick count so that the labels are a bit less dense.

And here's where I'm leaving things for now:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/finished-recipe.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Adding some additional changes to our axis styling can help get this concept to a good point.</figcaption>
    </figure>
</div>

---

## Wrapping-up

Well, I hope that you stuck with me to this point, have enjoyed the journey, and may even have a few ideas of your own!

As mentioned above, this has been an interesting study for me, and this is probably more of a jumping-off point for further designs. It's certainly taught me that some powerful effects that (while they might look simple) can be the product of a decent amount of development work to get it working nicely for your readers.

This is definitely a lot of effort to go to, but <a href="https://gist.github.com/dm-p/7976dc964437124ecf6830a74d9b2e03" target="_blank">hopefully the template for this work</a> may help expedite this for anyone who wishes to push it further, or come up with more effective and efficient means to replicate the approach. Please let me know if you do find anything out, of if you're able to use this in your own designs - as always, I'd love to hear from you if you're getting stuff done with Deneb.

Thanks as awlays for reading,

DM-P

---

<br/><br/><br/><br/><br/>

## Postscript: Attempting the linear algebra via DAX

> As discussed above, you can do the linear algebra with DAX, and because this formed a lot of my initial research for this article, I have left it in for anyone who may find it useful, or wish to attempt (and hopefully succeed) where I have failed.
>
> I have a feeling that this could be done more elegantly by using calculation groups, but we're already throwing enough at you withough having to worry about them too. For the purposes of illustration, we'll do everything with regular measures inside Power BI Desktop.

I have a simple data model, but key to this are the following features:

- I have a [Date] table in which I have a [Month] column. This gives all date records the start of month value as a date date type, e.g.:
- I have a [Sales] table with all of my sales transactions.
- There are some other dimension tables, but they aren't relevant to the calculations we're going to be doing (and they should support slicing and dicing by these other dimensions).

Our sales measures are defined as follows:

<pre>Sales&nbsp;=<br><span class="Keyword" style="color:#035aca">SUM</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;Sales[Sales&nbsp;Amount]&nbsp;<span class="Parenthesis" style="color:#808080">)</span><br>
Sales&nbsp;PY&nbsp;=<br><span class="Keyword" style="color:#035aca">CALCULATE</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;[Sales],&nbsp;<span class="Keyword" style="color:#035aca">DATEADD</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;'Date'[Date],&nbsp;<span class="Number" style="color:#EE7F18">-1</span>,&nbsp;<span class="Keyword" style="color:#035aca">YEAR</span>&nbsp;<span class="Parenthesis" style="color:#808080">)</span>&nbsp;<span class="Parenthesis" style="color:#808080">)</span><br></pre>

#### Supporting measures for Date logic

As mentioned above, the [Month] column aggregates the visual dataset to a monthly grain, and is date-types, so will aggregate our measures at the correct level of detail. That being said, I'm going to depend on it a few times, so I'm also going to declare a measure that handles the aggregation at the row-level we need:

<pre>Month&nbsp;Current&nbsp;Context&nbsp;=<br><span class="Keyword" style="color:#035aca">MAX</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;'Date'[Month]&nbsp;<span class="Parenthesis" style="color:#808080">)</span><br></pre>

And, a measure to get the following Month's date:

<pre>Month&nbsp;Following&nbsp;Context&nbsp;=<br><span class="Keyword" style="color:#035aca">CALCULATE</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;[Month&nbsp;Current&nbsp;Context],&nbsp;<span class="Keyword" style="color:#035aca">DATEADD</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;'Date'[Date],&nbsp;<span class="Number" style="color:#EE7F18">+1</span>,&nbsp;<span class="Keyword" style="color:#035aca">MONTH</span>&nbsp;<span class="Parenthesis" style="color:#808080">)</span>&nbsp;<span class="Parenthesis" style="color:#808080">)</span><br></pre>

If we add these to a table visual, with our existing dataset fields, we have the following:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/dax-01-months.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Creating two measures to assist with reusability of the current month row context and the month start value for the following month's row context.</figcaption>
    </figure>
</div>

#### Supporting Sales measures

We can now use these measures to derive the 'following month' values for each of our base measures:

<pre>Sales&nbsp;(Following&nbsp;Month)&nbsp;=<br><span class="Keyword" style="color:#035aca">CALCULATE</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;[Sales],&nbsp;<span class="Keyword" style="color:#035aca">DATEADD</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;'Date'[Date],&nbsp;<span class="Number" style="color:#EE7F18">+1</span>,&nbsp;<span class="Keyword" style="color:#035aca">MONTH</span>&nbsp;<span class="Parenthesis" style="color:#808080">)</span>&nbsp;<span class="Parenthesis" style="color:#808080">)</span><br>
Sales&nbsp;PY&nbsp;(Following&nbsp;Month)&nbsp;=<br><span class="Keyword" style="color:#035aca">CALCULATE</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;[Sales&nbsp;PY],&nbsp;<span class="Keyword" style="color:#035aca">DATEADD</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;'Date'[Date],&nbsp;<span class="Number" style="color:#EE7F18">+1</span>,&nbsp;<span class="Keyword" style="color:#035aca">MONTH</span>&nbsp;<span class="Parenthesis" style="color:#808080">)</span>&nbsp;<span class="Parenthesis" style="color:#808080">)</span><br></pre>

Now we have a way of accessing the next row's values, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/dax-02-following.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">By calculating additional measures for [Sales] and [Sales&nbsp;PY] that access the following month's value, we will have the values we need to calculate the slope of the line segment.</figcaption>
    </figure>
</div>

#### Calculating the slope of each line segment

We can now use these measures to create further measures that will derive the slope for our [Sales] and [Sales Y] lines:

<pre>Sales&nbsp;Slope&nbsp;=<br><span class="Keyword" style="color:#035aca">VAR</span>&nbsp;<span class="Variable" style="color:#49b0af">Numerator</span>&nbsp;=&nbsp;[Sales&nbsp;(Following&nbsp;Month)]&nbsp;-&nbsp;[Sales]<br><span class="Keyword" style="color:#035aca">VAR</span>&nbsp;<span class="Variable" style="color:#49b0af">Denominator</span>&nbsp;=&nbsp;[Month&nbsp;Following&nbsp;Context]&nbsp;-&nbsp;[Month&nbsp;Current&nbsp;Context]<br><span class="Keyword" style="color:#035aca">RETURN</span><br><span class="indent4">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Keyword" style="color:#035aca">DIVIDE</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;<span class="Variable" style="color:#49b0af">Numerator</span>,&nbsp;<span class="Variable" style="color:#49b0af">Denominator</span>,&nbsp;<span class="Keyword" style="color:#035aca">BLANK</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span><span class="Parenthesis" style="color:#808080">)</span>&nbsp;<span class="Parenthesis" style="color:#808080">)</span><br>
Sales&nbsp;PY&nbsp;Slope&nbsp;=<br><span class="Keyword" style="color:#035aca">VAR</span>&nbsp;<span class="Variable" style="color:#49b0af">Numerator</span>&nbsp;=&nbsp;[Sales&nbsp;PY&nbsp;(Following&nbsp;Month)]&nbsp;-&nbsp;[Sales&nbsp;PY]<br><span class="Keyword" style="color:#035aca">VAR</span>&nbsp;<span class="Variable" style="color:#49b0af">Denominator</span>&nbsp;=&nbsp;[Month&nbsp;Following&nbsp;Context]&nbsp;-&nbsp;[Month&nbsp;Current&nbsp;Context]<br><span class="Keyword" style="color:#035aca">RETURN</span><br><span class="indent4">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Keyword" style="color:#035aca">DIVIDE</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;<span class="Variable" style="color:#49b0af">Numerator</span>,&nbsp;<span class="Variable" style="color:#49b0af">Denominator</span>,&nbsp;<span class="Keyword" style="color:#035aca">BLANK</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span><span class="Parenthesis" style="color:#808080">)</span>&nbsp;<span class="Parenthesis" style="color:#808080">)</span><br>
</pre>

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/dax-03-slope.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">The slope value for each measure can now be calculated.</figcaption>
    </figure>
</div>

We can now see that we have a slope value for each measure. A negative value means the line "falls" (higher to lower value), whereas a positive value indicates a "rise" (lower to higher").

What's cool is that even though we might normally use numbers, Date/time values support arithmetic, so our values will be correct.

#### Calculating the y-intercept of each line segment

Our final component to be able to calculate the coordinates of our points is the y-intercept, which is the y (or measure) value that would be present if x (or [Month]) = 0:

<pre>Sales&nbsp;Y-Intercept&nbsp;=<br>[Sales]&nbsp;-<span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;[Sales&nbsp;Slope]&nbsp;*&nbsp;[Month&nbsp;Current&nbsp;Context]&nbsp;<span class="Parenthesis" style="color:#808080">)</span><br>
Sales&nbsp;PY&nbsp;Y-Intercept&nbsp;=<br>[Sales&nbsp;PY]&nbsp;-<span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;[Sales&nbsp;PY&nbsp;Slope]&nbsp;*&nbsp;[Month&nbsp;Current&nbsp;Context]&nbsp;<span class="Parenthesis" style="color:#808080">)</span><br></pre>

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/dax-04-intercept.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">We can now calculate our y-intercept for each measure; the y-value where x = 0.</figcaption>
    </figure>
</div>

These numbers will be quite extreme - relative to the measures - as, we're looking at such a small window of time, and time = 0 is quite a large span! Either way, we know how our straight line projects, and we now have all the moving parts to calculate our intersection points.

#### Calculating the intersection points

[As we mentioned above](#working-out-the-intersection-points), the formulas to calculate the coordinates reuse some common logic, so we'll create three measures: one for this centralised calculation, and then one each for the x ([Month]) and y (measure) values where intersection could occur:

<pre>Sales&nbsp;Intersection&nbsp;(Base)&nbsp;=<br><span class="Keyword" style="color:#035aca">DIVIDE</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span><br><span class="indent4">&nbsp;&nbsp;&nbsp;&nbsp;</span>[Sales&nbsp;PY&nbsp;Y-Intercept]&nbsp;-&nbsp;[Sales&nbsp;Y-Intercept],<br><span class="indent4">&nbsp;&nbsp;&nbsp;&nbsp;</span>[Sales&nbsp;Slope]&nbsp;-&nbsp;[Sales&nbsp;PY&nbsp;Slope],<br><span class="indent4">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Keyword" style="color:#035aca">BLANK</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span><span class="Parenthesis" style="color:#808080">)</span><br><span class="Parenthesis" style="color:#808080">)</span><br>
Sales&nbsp;Intersection&nbsp;(x)&nbsp;=<br><span class="Keyword" style="color:#035aca">VAR</span>&nbsp;<span class="Variable" style="color:#49b0af">Point</span>&nbsp;=<br><span class="indent4">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Keyword" style="color:#035aca">CONVERT</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;[Sales&nbsp;Intersection&nbsp;(Base)],&nbsp;<span class="Keyword" style="color:#035aca">DATETIME</span>&nbsp;<span class="Parenthesis" style="color:#808080">)</span><br><span class="Keyword" style="color:#035aca">RETURN</span><br><span class="indent4">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Keyword" style="color:#035aca">IF</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span><br><span class="indent8">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Variable" style="color:#49b0af">Point</span>&nbsp;&gt;&nbsp;[Month&nbsp;Current&nbsp;Context]<br><span class="indent8">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="indent4">&nbsp;&nbsp;&nbsp;&nbsp;</span>&amp;&amp;&nbsp;<span class="Variable" style="color:#49b0af">Point</span>&nbsp;&lt;&nbsp;[Month&nbsp;Following&nbsp;Context],<br><span class="indent8">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Variable" style="color:#49b0af">Point</span><br><span class="indent4">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Parenthesis" style="color:#808080">)</span><br>
Sales&nbsp;Intersection&nbsp;(y)&nbsp;=<br><span class="Keyword" style="color:#035aca">VAR</span>&nbsp;<span class="Variable" style="color:#49b0af">Point</span>&nbsp;=<span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;[Sales&nbsp;Slope]&nbsp;*&nbsp;[Sales&nbsp;Intersection&nbsp;(Base)]&nbsp;<span class="Parenthesis" style="color:#808080">)</span>&nbsp;+&nbsp;[Sales&nbsp;Y-Intercept]<br><span class="Keyword" style="color:#035aca">RETURN</span><br><span class="indent4">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Keyword" style="color:#035aca">IF</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;[Sales&nbsp;Intersection&nbsp;(x)],&nbsp;<span class="Variable" style="color:#49b0af">Point</span>&nbsp;<span class="Parenthesis" style="color:#808080">)</span><br></pre>

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/hill-valley/dax-05-intersect.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">We now have the values where intersection may occur between one row to the next.</figcaption>
    </figure>
</div>

The [Sales Intersection (Base)] measure returns a numeric value, representing the date and time of an intersection point, and is essentially the x-value. However, because an intersection point may occur outside the range of our line segment, the logic in the [Sales Intersection (x)] and [Sales Intersection (y)] measures ensures that if this is outside the range, then a `BLANK()` value is returned, and we only see results in the output where we may need such a point to be plotted.

---

And this is where I got to before deciding to change tack to what's documented up-top, so if yo wish to attempt to take it further...

<div class="text-center">
    <figure class="figure">
        <img src="https://i.imgur.com/guy4k14.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Well, I definitely am :)</figcaption>
    </figure>
</div>
