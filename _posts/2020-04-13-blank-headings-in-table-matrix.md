---
layout: post
title:  "Creating Blank Table & Matrix Headings in Power BI"
author: daniel
categories: [ Exploits, Visuals ]
tags: [ table, matrix, powerbi, unicode ]
image: 'assets/images/blank-table-heading/blog-image.png'
description: "Options for hiding a column heading in Power BI tables and matrices in situations where it might be redundant."
hidden: false
permalink: examples/blank-table-heading
---

I love the conditional formatting in Power BI tables and matrices, particularly when it comes to data bars - they're a great way to produce space-efficient visuals. What I'm less of a fan of is when you combine labels and bars in the same cell, and you can't easily resolve the size of the bar from the number:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/blank-table-heading/bars-and-label.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Data bar conditional formatting w/labels in the same cell</figcaption>
    </figure>
</div>

In situations when the label is outside of the bar, this is perfectly legible. However, when the label is entirely inside the data bar, or it spans the boundaries (such as in the *Central* and *East* categories above), it gets a little trickier.

----
We can make changes to the text colour or the data bar colour, or even the alignment of the value, but this is currently an all-or-nothing approach. For instance, the following looks pretty good:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/blank-table-heading/bars-with-lighter-shading.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Modifying bar colour and value alignment, to improve readability</figcaption>
    </figure>
</div>
----

Now, let's say we've started up a *North* region, and as they're an emerging market, their sales are a bit lower:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/blank-table-heading/bars-with-lighter-shading-emerging.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">An emerging market, <b>North</b>, with value obscuring the data bar</figcaption>
    </figure>
</div>

The lower value results in a data bar that sits behind the text and it can be seen, but can take a little more effort to resolve.

----
What might be nice, is if I can separate the label from the bar, so I can control elements of both. We might think this can be done by adding the measure again to the list of fields in the table. As this has the same ID in our model as the measure we've previously added, we basically get the same column again in our table and this will respond to any changes we make to the other, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/blank-table-heading/adding-same-measure-changing-props.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Adding the measure to the <b>Values</b> field and removing the values updates both columns...</figcaption>
    </figure>
</div>

----
We can work around this by creating a new measure that is a duplicate of our existing one, e.g.:

```
$ Amount (Bars) = [$ Amount]
```

**EDIT**: there's some additional things you can do with the measure to prevent the total being displayed in the table if you're using them. Refer to the [appendix down below](#appendix---handling-totals) for this.

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/blank-table-heading/adding-independent-measure.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Adding a measure with the same result allows us to configure columns independently</figcaption>
    </figure>
</div>


This is looking better, but that column heading for the data bars looks redundant. It would be ideal if we could remove it. We can't condition its visibility at this time, but there are a couple of approaches we can take:

## Option 1: Format Column Heading

Fortunately, We can modify our heading for this column to match our background. In my case, this is the same as the report, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/blank-table-heading/modifying-header-colours.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Modifying our column heading formatting to "hide" the name</figcaption>
    </figure>
</div>

What we did here was:

* Selected the *Field formatting* menu from the visual properties
* Selected the **$ Amount (Bars)** measure from the dropdown
* Set the *Font color* to the same as our visual background - **`#ffffff`**
* Set the *Apply to header* toggle

----
## Option 2: Exploiting Unicode Whitespace

The other option is a little more of a quirk, and may be fixed later on.

It might have occurred to you to try renaming the measure to [empty], or putting a space in the name, but Power BI will helpfully prevent this, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/blank-table-heading/renaming-columns-with-whitespace.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Attempting to rename column by deleting its label, or replacing with spaces</figcaption>
    </figure>
</div>

----
There is a loophole to this: we can insert a <a href="https://en.wikipedia.org/wiki/Zero-width_space" target="_blank">zero-widthspace</a> unicode character as the renamed measure. This is not easy to type directly, so we can obtain from a website such as <a href="[https://beautifuldingbats.com/whitespace/](https://qwerty.dev/whitespace/)" target="_blank">qwerty.dev</a> and paste it into the measure name for the visual, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/blank-table-heading/copy-pasting-whitespace-into-measure-name.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Obtaining unicode whitespace from an external website and pasting into the visual</figcaption>
    </figure>
</div>

----
It's worth re-stating that this second approach works currently, but may be shored-up in future, as it's exploting Power BI's sanitisation of the measure name for the visual. While the first approach is the more 'correct' way to manage this in Power BI, the latter may be something you can add to your toolbox when looking for creative solutions.

Until next time,

DM-P

----

## Appendix - Handling Totals

Since publishing, I've had <a href = "https://twitter.com/InsightsMachine/status/1249786337911140352" target="_blank">this very useful feedback on Twitter</a>:

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Good idea. <a href="https://twitter.com/the_d_mp?ref_src=twsrc%5Etfw">@the_d_mp</a> Might be worth updating your blogpost to show how to use IF(HASONEVALUE to accomplish this in need. <a href="https://t.co/5qRbTIeDl3">pic.twitter.com/5qRbTIeDl3</a></p>&mdash; Jeff Weir(d) (@InsightsMachine) <a href="https://twitter.com/InsightsMachine/status/1249786337911140352?ref_src=twsrc%5Etfw">April 13, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

If we enabled totals for our example, this would currently look as follows:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/blank-table-heading/total-showing.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Artefact of not handling totals for redundant column</figcaption>
    </figure>
</div>

Conditional formatting for totals is on the roadmap and should be with us soon, but we can fulfil Jeff's wishes by modifying our measure to consider the context we are in so that it doesn't display at the total level. For our example, this would look as follows:

```
$ Amount (Bars) = IF(HASONEVALUE(Data[Region]), [$ Amount])
```

Our table now looks a quite a bit nicer as a result, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/blank-table-heading/total-not-showing.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Cleaning up our measure with a <b>HASONEVALUE</b> check</figcaption>
    </figure>
</div>
