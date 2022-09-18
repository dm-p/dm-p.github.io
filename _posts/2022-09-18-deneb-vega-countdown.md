---
layout: post
title: "Building a dynamic countdown in Power BI with Deneb & Vega"
author: daniel
categories: [Design, Prototyping]
tags: [deneb, vega, playaxis]
description: "Using the flexibility of Vega events to indicate when a Play Axis will next refresh."
image: "assets/images/deneb/countdown-timer/countdown-timer-banner.png"
featured: false
toc: false
permalink: deneb_countdown_timer
---

I got asked today if it was possible to use the <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA200001930?tab=overview" target="_blank">HTML Content visual</a> to display a countdown in a Power BI report, where the countdown would indicate how long until the <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/wa104380981?tab=overview" target="_blank">Play Axis visual</a> was going to do its next selection.

For those not in the know, the Play Axis is a visual that you can add a column to, and this will sequentially slice the report page based on an interval that you configure in that visual's properties. So, the idea was to be able to provide a little extra feedback on the page as to when the next slice would be likely to occur.

I thought about the problem for a bit, and while it's possible to do something like this in HTML and JavaScript, <a href="https://www.html-content.com/reference/limitations#what-doesnt-or-might-partially-work" target="_blank">I don't really support JavaScript</a> in the HTML Content visual, and I probably won't. Some people may have figured out a way of adding it, and that's fine if it works for them... it just requires a lot of support and functionality that I don't have the free time to dedicate to supporting how I would like it to work in the visual.

---

## But... I like solving problems!

So, I started thinking that this is probably an easy job using <a href="https://deneb-viz.github.io/" target="_blank">Deneb</a> and <a href="https://vega.github.io/vega/" target="_blank">Vega</a>. I tend to work with and blog about Vega-Lite, but you can do a lot more with Vega, if you're willing to put in the work. <a href="https://vega.github.io/vega/docs/event-streams/" target="blank">Vega has Event Streams</a> as part of its language, which are a mixture of things that tie to the underlying browser, and some of the things happening inside the Vega view, and this is how we can build some functionality based on timer events in the current page.

So, if you've been putting off looking at Vega, this post can give you a very simple intro to some of the concepts.

---

## Report setup

I've set up a simple report page to illustrate this example. We have a Play Axis that's been set up to _Auto Start_, _Loop_ and have a _Time_ of 10,000 ms (10 seconds). This will cycle through a simple multi-row card that shows sales by month, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/countdown-timer/countdown-timer-setup.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">A simple setup of the Play Axis custom visual, with a multi-row card. The Play Axis is set to automatically start, loop and select the next slice of our Month/Year column every 10,000ms (10 seconds).</figcaption>
    </figure>
</div>

---

## Approaching the Vega specification

Our approach for how we'll use Vega to display a timer is as follows:

1. Define our interval, which will match the interval of the Play Axis.
2. Calculate the reference time for the current slicer column.
3. Calculate our counter value, based on the difference from the current time to the reference time. This will be updated using a timer.
4. Calculate the display text, based on this value.
5. Create a text mark to display the text.

Because our logic is evaluated based on timer events, we can ensure that the text mark is updated with the correct values. And, when the Play Axis performs its next filter, the Deneb visual will be updated and start counting down again.

---

## Adding Deneb and a measure

We'll start by adding the Deneb visual to our report canvas and assigning it a measure so that there is some data. This can be anything from your report, as we're not going to use it (it's just that Deneb requires some data so that you can start editing). I personally just use something like the following:

<pre>Dummy&nbsp;=<br><span class="Keyword" style="color:#035aca">BLANK</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span><span class="Parenthesis" style="color:#808080">)</span><br></pre>

As you can see, it is a most exciting measure 🙂

---

## Creating the initial specification

With some 'data', we'll now click on the visual header and select _Edit_ to open the advanced editor. In the _Create New Specification_ dialog, we'll chose _Vega_ and _Empty_ as our template, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/countdown-timer/countdown-timer-empty-template.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Select 'Vega' as the provider and '[empty]' as the template in the <i>Create New Specification</i> dialog.</figcaption>
    </figure>
</div>

Click the _Create_ button, and this will present you with the editor, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/countdown-timer/countdown-timer-01-empty-editor.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">After clicking the <i>Create</i> button, we now have an editor with an empty Vega specification.</figcaption>
    </figure>
</div>

---

## Creating the interval and reference time

In our specification, we'll add a `signals` property (which is an array), and two initial entries, e.g.:

```
{
  "data": [{ "name": "dataset" }],
  "signals": [
    {
      "name": "interval",
      "init": "10000"
    },
    {
      "name": "startTime",
      "init": "now()"
    }
  ],
  "marks": []
}
```

Here, we've added:

1. A signal named `interval`, which has an initial value of `10000` (which will match the interval of our Play Axis slicer).
2. A signal named `startTime`, which captures the time the visual last updated.

We can inspect the values of these signals using the _Signals_ tab in the Debug pane, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/countdown-timer/countdown-timer-02-initial-signals.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">We can inspect the values of our new signals using the <i>Signals</i> tab in the Debug pane.</figcaption>
    </figure>
</div>

---

## Adding and updating the counter value

Now that we have the start time and the interval, we can write logic to take them into account and this will handle the lion's share of what we want the countdown to do. Add the following to the `signals` array in the specification:

```
{
  ...
  "signals": [
    ...
    {
      "name": "counter",
      "init": "interval",
      "on": [
        {
          "events": {
            "type": "timer",
            "throttle": 10
          },
          "update": "round((interval - (now() - startTime))/1000)"
        }
      ]
    }
  ],
  ...
}
```

Here, we've added a new signal named `counter`, which will run every 10ms, and update its own value with the following logic:

1. Calculate the difference in milliseconds between the current time and the `startTime`).
2. Subtract this from the `interval` value (because we are counting down).
3. Divide this by 1,000 (to get seconds).
4. Round this to the nearest whole number.

As we're doing a very simple countdown, it makes sense to calculate whole seconds at this point, but you could keep as milliseconds and use more logic in the presentation part of the specification.

When we apply our changes, we can now see the `counter` signal in the _Signals_ tab, and that it changes to a new value every 1 second or so, e.g.:

<div style="text-align: center;">
<video controls width="80%">
<source src="/assets/mp4/deneb/countdown-timer/countdown-timer-counter-signal-update.mp4" type="video/mp4">
Sorry, your browser doesn't support embedded videos.
</video>
</div>
<br/>

> In the above example, the Play Axis slicer is still affecting our visual (even though Deneb is in focus mode), so the `startTime` value is also updating. You may wish to disable the Play Axis temporarily if this is something you'd prefer not to happen.

---

## Calculating the display text

We'll ad one more signal to the array, which will handle the simple display logic for our text mark:

```
{
  ...
  "signals": [
    ...
    {
      "name": "seconds",
      "update": "counter + ' second' + (counter == 1 ? '' : 's')"
    }
  ],
  ...
}
```

This creates a signal called `seconds`, which simply creates the value, _'n seconds'_ (if `counter` is anything other than `1`), or _'n second'_ (if `counter == 1`).

We can now inspect the _Signals_ tab again, and see this value updating too, e.g.:

<div style="text-align: center;">
<video controls width="80%">
<source src="/assets/mp4/deneb/countdown-timer/countdown-timer-counter-seconds-update.mp4" type="video/mp4">
Sorry, your browser doesn't support embedded videos.
</video>
</div>
<br/>

> This is a very simple example, but hopefully gives you an idea as to how you can think about conditional formatting logic in your expressions.

We now just have to take care of the display portion of our specification.

---

## Creating the text mark

Our visual is just going to have a simple `text` mark, which displays the `seconds` value, so we'll set this up. We'll now add an entry to the `marks` array, e.g.:

```
{
  ...
  "marks": [
    {
      "type": "text",
      "encode": {
        "enter": {
          "x": {"value": 10},
          "y": {"signal": "height/2"},
          "baseline": {
            "value": "middle"
          }
        },
        "update": {
          "text": {
            "signal": "seconds"
          }
        }
      }
    }
  ]
}
```

With Vega, all properties are done in the `encode` channel, so the key things to think about are that:

1. The `enter` property dictates what should be done when the mark is drawn for the first time.
2. The `update` property dictates what should be done when the mark receives updates.

> These concepts are covered in more detail <a href="https://vega.github.io/vega/docs/marks/#visual-encoding" target="_blank">in Vega's marks documentation</a>.

To summarise what we're assigning here:

1. Horizontally position the mark 10 pixels from the left (x).
2. Vertically position the mark in the centre.
3. Ensure the text baseline is in the middle also.
4. Supply the `seconds` value each time the update runs.

In the _Config_ pane, I'm just going to add some cosmetic styling to the text mark:

```
{
  "text": {
    "font": "Segoe UI",
    "fontSize": 16,
    "fill": "#605E5C"
  }
}
```

When we apply our changes, we now get something that will be visible to our user, e.g.:

<div style="text-align: center;">
<video controls width="40%">
<source src="/assets/mp4/deneb/countdown-timer/countdown-timer-counter-mark-simple.mp4" type="video/mp4">
Sorry, your browser doesn't support embedded videos.
</video>
</div>
<br/>

It's probably a good time to check and review how things are looking on the report.

---

## Reviewing our specification on the canvas

We can now save our changes, go back to the report canvas, and resize and position our visual so that's where we want it, e.g.:

<div style="text-align: center;">
<video controls width="40%">
<source src="/assets/mp4/deneb/countdown-timer/countdown-timer-counter-first-review.mp4" type="video/mp4">
Sorry, your browser doesn't support embedded videos.
</video>
</div>
<br/>

We can see that the multi-row card, and the simple timer we've built refresh when the Play Axis moves on to its next filter context. This is a fairly rudimentary visual, but hopefully illustrates some of the basic concepts of Vega and its event handling without being too complex for the uninitiated.

We could also hide the Play Axis from the canvas at this point if we so wished.

---

## A quick improvement after review

Having seen this in situ, I'm just going to make a small change to the `seconds` signal so that it's a bit more obvious what this means to the reader:

```
{
  ...
  "signals": [
    ...
    {
      "name": "seconds",
      "update": "counter + ' second' + (counter == 1 ? '' : 's') + ' until next selection'"
    }
  ],
  ...
}
```

I've also set the x-position of the mark to zero, so that it lines up with the card, but that's just a personal preference. However, this now makes a bit more sense when viewed on the canvas, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/countdown-timer/countdown-timer-final-tweaks.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">After changing the text of the <code>seconds</code> signal, our compact visual makes a bit more sense on the canvas.</figcaption>
    </figure>
</div>

---

## Complete recipe

Putting everything together, our specification looks as follows:

```
{
  "data": [{ "name": "dataset" }],
  "signals": [
    {
      "name": "interval",
      "init": "10000"
    },
    {
      "name": "startTime",
      "init": "now()"
    },
    {
      "name": "counter",
      "init": "interval",
      "on": [
        {
          "events": {
            "type": "timer",
            "throttle": 10
          },
          "update": "round((interval - (now() - startTime))/1000)"
        }
      ]
    },
    {
      "name": "seconds",
      "update": "counter + ' second' + (counter == 1 ? '' : 's') + ' until next selection'"
    }
  ],
  "marks": [
    {
      "type": "text",
      "encode": {
        "enter": {
          "x": { "value": 0 },
          "y": { "signal": "height/2" },
          "baseline": {
            "value": "middle"
          }
        },
        "update": {
          "text": {
            "signal": "seconds"
          }
        }
      }
    }
  ],
  "config": {
    "text": {
      "font": "Segoe UI",
      "fontSize": 16,
      "fill": "#605E5C"
    }
  }
}
```

---

I've been tempted to produce something quite elaborate, as once the problem is solved there's all kinds of places you can take the design. However, I'll leave all that fun stuff up to you. I hope that this use case might have a place in your report writing, and if not, then it may at least expose you to some more of Vega's functionality.

Thanks as always for reading,

DM-P
