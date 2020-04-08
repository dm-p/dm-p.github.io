---
layout: post
title:  "No-Code Custom Visuals with Small Multiples"
author: daniel
categories: [ Custom Visual, Example, Charticulator, Charts.PowerBI.Tips ]
tags: [ smallmultiples ]
image: 'assets/images/charticulator-sm/blog-image.png'
description: "Using Charticulator or the Charts.PowerBI.Tips tool to build our own Power BI custom visuals with small multiples."
hidden: false
permalink: examples/charticulator-sm
---

This article is a (significantly) longer-form write-up of a <a href="https://twitter.com/the_d_mp/status/1234937840322039808" target="_blank">Twitter thread I published a few weeks back</a>.

As we have a bit more room to breathe in the medium of a blog post, I wanted to put together a tutorial with some extra framing around the visualisation techniques and choice of tooling. So if you know what small multiples are, then feel free to [skip ahead to the bit where we start doing stuff below](#the-no-code-alternative).

----
# Small Multiples?

You may or may not have come across this concept, so let's do a quick intro: small multiples are an incredibly powerful tool when it comes to visualising data. If you haven’t heard of them before, you might have seen them under another name: *trellis*, *lattice*, *grid*, *panel*, or *multi-variate analysis*.

Whatever your flavour, it is ultimately a series of charts with the same scale and axes, which allow you to easily compare them.

----

For the uninitiated, I'll illustrate with an example.

I live in New Zealand and like pretty much anyone else with an eye on the news, I’m following our COVID-19 pandemic status very closely, and our <a href="https://www.health.govt.nz/our-work/diseases-and-conditions/covid-19-novel-coronavirus/covid-19-current-situation/covid-19-current-cases/covid-19-current-cases-details" target="_blank">Ministry of Health regularly publishes these data</a>.

**Like most people, I’m not an epidemiologist**, and my rudimentary plots are for the purposes of demonstrating the concepts in this tutorial.

The Excel workbook is pretty easy to pull into Power BI, so I've grabbed this as of 6 April 2020 our time and transformed this into a simple star schema of our total cases to date, including a simple measure for # cumulative cases:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/data-model.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Data model for our example</figcaption>
    </figure>
</div>
----
I can then plot # Cumulative Cases on a line chart:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/cumulative-line-chart.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Cumulative line chart of total reported cases (Probable + Reported) as of 9AM 6th April 2020</figcaption>
    </figure>
</div>
----
I might want to then explore this further, perhaps by geography. Our data is segmented into District Health Boards (DHBs), so I *could* add the DHB to the chart as the *Legend*...

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/cumulative-line-chart-legend.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Our line chart, broken into DHB on the <i>Legend</i></figcaption>
    </figure>
</div>

Ouch. We can see the breakdown, but it’s standing room only! There’s also several DHBs that don’t fit on the legend and this, in conjunction with the many series, makes it hard for the reader to derive insights easily. We want our readers to derive insights easily 😊

----
What would be great, is if we could project this into lots of little charts, like so:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/small-multiples-simple.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">A smaller chart, multiplied by the number of unique category values... small multiples!</figcaption>
    </figure>
</div>

There are currently 20 DHBs in New Zealand - each value of DHB becomes a category, which in turn produces a chart with the same ranges (Date) and values (# Cumulative Cases) for their axes.

Without spending a huge amount of time staring at it, we can easily see that some DHBs started reporting cases quite a way before others, some currently have a much slower rate of growth than others.

----
# How Can I Make these Myself?

If you're learning this all for the first time, you might be wondering how to do this in Power BI. At this time, small multiples are not a part of the tooling and - if using the core visuals - the maker will need to craft them manually:

* Create a chart with a filter on the category you want to treat as a small multiple
* Copy/paste filter as many as you need
* Manually set the x/y axis start/end values as appropriate for your data

This is repetitive - and like anything that needs to be done manually - will need to be reviewed to ensure everything is consistent between visuals.

You'll also need to ensure that you continually update the axis values as things change, as well as manage any new values that might emerge for the category you're using as a small multiple. More dynamic elements of reporting, such as responding to slicers, also become challenging to incorporate.

The above challenges were actually a catalyst for me to learn the custom visuals framework and <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA104381711" target="_blank">produce one of my own</a>. This is a somewhat extreme solution, and not everyone has this amount of patience or time on their hands.

There are many other great custom visuals in the marketplace that provide small multiples as a capability:

* <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA200000805" target="_blank">Zebra BI Charts</a>
* <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA104380898" target="_blank">Infographic Designer</a>
* <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA104382030" target="_blank">Craydec Regression Chart</a>
* <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA104381810" target="_blank">3AG Systems - Column Chart With Small Multiples</a>
* Visuals by Klaus Birringer:
    * <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA104381977" target="_blank">Ultimate KPI Card</a>
    * <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA200000839" target="_blank">Ultimate Stacked</a>
    * <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA104381140" target="_blank">Ultimate Variance</a>
    * <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA104381810" target="_blank">Ultimate Waterfall</a>
* Visuals by Akvelon:
    * <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA104381805" target="_blank">100% Stacked Bar Chart</a>
    * <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA104381818" target="_blank">100% Stacked Column Chart</a>
    * <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA104381822" target="_blank">Clustered Bar Chart</a>
    * <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA104381939" target="_blank">Small Multiple Line Chart</a>
    * <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/WA104381825" target="_blank">Stacked Column Chart</a>

Additionally, you can also use the core <a href="https://docs.microsoft.com/en-us/power-bi/desktop-r-visuals" target="_blank">R</a> and <a href="https://docs.microsoft.com/en-us/power-bi/desktop-python-visuals" target="_blank">Python</a> visuals to produce small multiples using their libraries. 

----
# The No-Code Alternative

If you haven't found a custom visual that suits your needs, and you're not looking to upskill into R or Python right now, you can also give <a href="http://charticulator.com/" target="_blank">Charticulator</a> a try. This is a tool from Microsoft Research that is primarily designed to create bespoke visual layouts for the web. While Power BI is not its primary target, the tool also has a utility to export designs as a Power BI custom visual. 

The team at <a href="https://powerbi.tips/" target="_blank">PowerBI.Tips</a> have <a href="https://charts.powerbi.tips" target="_blank">also made the tool available on their website</a>. They've just introduced the awesome **Nested Charts** functionality, which I guess is yet another way to describe small multiples! We're going to use this so that we can produce our very own no-code Power BI small line chart custom visual for the example above.

What follows is my preferred way of putting this together. You may have your own requirements and the awesome part of this is how you might apply your own creativity to your own version of this - I would encourage you to do this as soon as you get the hang of things. Here's my finished product:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/finished-recipe.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">What we're aiming for...</figcaption>
    </figure>
</div>

> I've [provided the assets from this tutorial at the bottom of the article](#assets-and-materials) if you want to skip ahead and audition the finished product before getting into things.

A couple of things to bear in mind:

* Charticulator is an external tool designed for bespoke layouts and as such is not as responsive as core Power BI visuals might be.
* Sometimes a design may not work perfectly inside Power BI, so some experimentation and refinement may be required.
* If your data changes, such as the scenarios above where date and measures may expand, nested charts don't currently auto-adjust.
* In these cases you will need to modify your visual configuration, re-export and re-import.

----
# Walkthrough

## Accessing the Tool

You can do one of the following:

* From the <a href="https://powerbi.tips/" target="_blank">PowerBI.Tips</a> home page, selecting **NO CODE - CUSTOM VISUALS** from the **TOOLS** menu.
* Going directly to <a href="https://charts.powerbi.tips" target="_blank">charts.powerbi.tips</a>

You'll be greeted with the main screen, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-home.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Landing page</figcaption>
    </figure>
</div>

----
## Prepping Data for the Visual

The tool requires you to load a comma-separated values (CSV) representation of the data you wish to work with. I can do this by going into my Power BI model and creating a table visual with the 3 fields I want:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/table-for-export.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Using a table visual to get the data I need</figcaption>
    </figure>
</div>

I can then export this by selecting the ellipsis (...) in the visual header and then **Export data**.

----
## Loading our Data

We then load our CSV data into the tool. Once we do this and click **Done**, the main canvas will be displayed, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-data-load.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Loading our CSV data into the tool</figcaption>
    </figure>
</div>

With the canvas available, we should see our fields in the *Columns* area of the screen - also know as the <a href="http://charticulator.com/docs/getting-started.html#components" target="_blank">Dataset Panel</a>, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-columns.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Our data columns, showing in the canvas</figcaption>
    </figure>
</div>

----
## Clearing the Canvas

We want to make as much space available for the visual as possible, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-canvas-prep.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Cleaning-up the canvas</figcaption>
    </figure>
</div>

What we did above was:

* Delete the auto-generated title by clicking on it and pressing \[Delete\].
* Drag the visual <a href="http://charticulator.com/docs/getting-started.html#terminology" target="_blank">guides</a> to reduce the amount of whitespace around the edges.

----
## Adding our Mark

Charticulator uses <a href="http://charticulator.com/docs/getting-started.html#terminology" target="_blank">glyphs</a> to represent a row of data. We can add multiple <a href="http://charticulator.com/docs/getting-started.html#terminology" target="_blank">marks</a> to a glyph, so we'll add the *Nested Chart* mark to the *Glyph* editor:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-nested-chart-mark.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Adding our Nested Chart mark to our glyph</figcaption>
    </figure>
</div>

This is a bit hectic! The reason for this is that the tool works a little differently to Power BI: Power BI will aggregate your data down to unique values for each category, whereas the tool will show a glyph for every row of the loaded data, irrespective of whether values are the same or not. We'll fix this next.

----
## Grouping our Data

We need to make sure that we show one nested chart for each value of DHB instead:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-plot-segment-group.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Changing our plot segment grouping to <b>DHB</b></figcaption>
    </figure>
</div>

This looks a bit more manageable! What we did above was:

* Clicked the plot segment in the *Layers* panel.
* Changed its *Group By...* attribute to **DHB**.

----
## Accessing the Nested Chart

Now we have laid out our main chart how we want it, we need to access the Nested Chart:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-edit-nested-chart.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Changing our plot segment grouping to <b>DHB</b></figcaption>
    </figure>
</div>

We did this by:

* Clicking on the nested chart in the *Layers* panel.
* Clicking **Edit Nested Chart...** in the *Attributes* panel.

This will open a second instance of Charticulator in a new tab in your browser. This should look almost the same as your other tab, except for the presence of a new button above the *Columns* panel:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-save-nested-chart.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">The <b>Save Nested Chart</b> button</figcaption>
    </figure>
</div>

This is where we'll edit our nested chart.

----
## First-Time Nested Chart Behaviour

There’s currently a bug with the nested chart upon when it’s first opened, the layout isn’t correct. This will sync up correctly if you do anything inside the designer to make a change. I typically drag one of the canvas guides a small amount to refresh the display, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-edit-nested-chart-bug.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Making a small change to the Nested Chart canvas to ensure it's synced with the main chart</figcaption>
    </figure>
</div>

Once we do that, our canvas will update to match what we saw from the main chart earlier on.

The nested chart tends to be smaller than a full chart, so I also zoom the canvas in a bit.

----
## Small Multiple Heading

We want our reader to know which category they are viewing the chart for, so we'll update the chart title, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-edit-nested-chart-title.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Updating the Nested Chart title to match our small multiple value</figcaption>
    </figure>
</div>

What we did here was:

* Clicked on the title in the *Layers* panel (we could also click on it in the canvas)
* Dragged the **DHB** column to the *Test* field in the *Attributes* panel

You should notice that the title has been updated to match the name of the first value from the group.

----
## Checking your Changes in the Main Chart

Any time we want to see how the main chart is looking, we can first click the **Save Nested Chart** button in the top-left corner, and navigate back to the browser tab containing the main chart, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-edit-nested-chart-check-main.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Saving and navigating back to our main chart, in order to check our changes</figcaption>
    </figure>
</div>

We can now see that the change we made above - to update the chart title - works for each instance of the Nested Chart 👍

----
## Adding Axes

Back in the Nested Chart, we'll add our **Date** field as the x-axis:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-edit-nested-chart-x-axis.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Adding the x-axis to our Nested Chart</figcaption>
    </figure>
</div>

What we did here was:

* Dragged the **Date** field to the plot segment's x-axis
* Modified the font sizing by clicking the plot segment, and clicking the ellipsis (...) under *X Axis* in the *Attributes* panel
* Dragged the guides to give us the best fit for the font size and Nested Chart canvas 

We’ll leave the date format as-is for now; this can be an exercise for you to do once we’ve got the chart working how we want it.

----

Now, we'll set up the y-axis with our **# Cumulative Cases**:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-edit-nested-chart-y-axis.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Adding the y-axis to our Nested Chart</figcaption>
    </figure>
</div>

What we did here was:

* Dragged the **# Cumulative Cases** field to the plot segment's y-axis
* Modified the font sizing by clicking the plot segment, and clicking the ellipsis (...) under *Y Axis* in the *Attributes* panel
* Dragged the guides to give us the best fit for the font size and Nested Chart canvas
* Made any necessary adjustments to the x-axis and Nested Chart guides to maximise space for the data later on

----
It’s tempting to have a peek at the main chart by saving the Nested Chart and navigating back. I recommend doing this a lot, to make sure things are progressing as you intend. Here’s the whole chart now:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-main-chart-after-axes.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Reviewing our main chart afted adding axes to our Nested Chart</figcaption>
    </figure>
</div>

----
## Adding Data Points

Now our axes are all good, we can add our data.

As mentioned above, a glyph represents each row of our data. In a Nested Chart, this is filtered down to the rows that match the grouped value.

To create a line chart, we need to do two key things: 

1. Create a mark for the glyph
2. Create links for to connect them together

Let's start with the mark:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-edit-nested-chart-data-mark.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Adding the data mark to our Nested Chart</figcaption>
    </figure>
</div>

What we did here was:

* Dragged a symbol to the *Glyph* canvas
* Changed its *Size* to a smaller number in the *Attributes* panel

Because the glyph represents each data point and we've added our axes, each mark is resolved to its correct x/y coordinates.

----
To connect the glyphs, we make use of the **Links** tool, with the default options e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-edit-nested-chart-data-link.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Using <b>Links</b> to connect our glyphs</figcaption>
    </figure>
</div>

----
We'll save and taken another quick peek at the main chart, now that we have something that shows our data:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-main-chart-after-links.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Our main chart, now that we've added our data</figcaption>
    </figure>
</div>

We’ve now got the crux of our visual in-place 😊

----
## Cosmetic Improvements

As my intended destination is Power BI and I'm pretty happy with my layout, I'm going to make some cosmetic changes to the Nested Chart, just to make it look a bit more at home.

#### Small Multiple Heading

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-edit-nested-chart-cosmetic-title.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Updating the Small Multiple Heading font and colour</figcaption>
    </figure>
</div>

What we did here was:

* Selected the title from the *Layers* panel
* Changed the *Font* to **Segoe UI Semibold** (note that while there is a font picker, it's quite limited, and you can manually type in the name of a known font on your system)
* Changed the *Color* to something a little less subtle than black. In this case I've used one of the Power BI theme defaults: **`#605e5c`**

----
### Axes

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-edit-nested-chart-cosmetic-axes.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Updating the font and colour for our axes</figcaption>
    </figure>
</div>

What we did here for each axis was:

* Selected the plot segment from the *Layers* panel
* In the *Attributes* panel, under the axis, clicked the ellipsis (...)
* Modified the *Line Color* to a lighter shade of grey- I've used **`#bdbdbd`** from the Power BI default theme
* Modified the *Tick Color* to match that of the title: **`#605e5c`**
* Modified the *Font Family* to **Segoe UI**

----
### Glyph Symbol

As like all creations, this is entirely based on personal preference. I want to hide the symbol representing the data points so that I just have the line provided by the links; we still need the symbol in our chart to ensure they get connected together.

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-edit-nested-chart-cosmetic-symbol.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Hiding the symbol so that we just have our line</figcaption>
    </figure>
</div>

What we did here was:

* Clicked the symbol in the *Layers* panel
* Changed its *Fill* to **(none)** in the *Attributes* panel

----
### Links

Finally, we're going to make our line stand out a bit more:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-edit-nested-chart-cosmetic-links.gif" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Styling our line and fixing its draw order</figcaption>
    </figure>
</div>

What we did here was:

* Clicked the link in the *Layers* panel
* Modified the *Color* in the *Attributes* panel - in this case, I've chosen the primary theme colour from the Power BI default theme: **`#118dff`**
* Modified the *Width* in the *Attributes* panel to make the line slightly thicker
* Dragged the link in the *Layers* panel to be drawn after the plot segment, meaning that it gets put on top

----
## Final Tweaks to Main Chart

I think we're now ready to take another look at our main chart, so we'll save the nested chart and navigate back:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-main-chart-finished.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Our main chart, after making our final cosmetic changes</figcaption>
    </figure>
</div>

That looks about right!

I'm going to make one final change, which may not be evident from this view - if we decide to slice our visual once we're in Power BI it may not organise correctly, so click the plot segment and set the vertical align to **Top** in the *Attributes* panel:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-sub-layout-top.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Changing our vertical layout for the plot segment</figcaption>
    </figure>
</div>

----
## Getting Our Visual into Power BI

We're now at a point where we can try our visual in Power BI.

### Safety First!

Once you've laoded into Power BI, you might want to make further tweaks and try again, so before we plough on ahead, it's a good idea to make sure you've saved your chart if you haven't already. Note that saving your chart will keep it in your browser's session and this **does not guarantee permanent storage**. If you want to truly keep your chart for future revisions, you can get it as a `.chart` file by doing the following:

* After saving your chart, click on the **Open** button in the toolbar
* Find your chart in the list, and click the **Download** button:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-download-chart.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Getting the .chart file for our work</figcaption>
    </figure>
</div>

What's really cool about the PowerBI.Tips tool is that if you're too excited to get to the Power BI part and forget to do this and export the visual as below, they automatically save the state of your chart at point of export for you in the list 👏

### Doing the Export

* From the toolbar, click the **Export** button
* Select **Power BI Custom Visual**
* Under the *Power BI Custom Visual Properties* section, fill in your details as needed, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/charticulator-visual-export.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Exporting our Power BI custom visual</figcaption>
    </figure>
</div>

* Click the **Power BI Custom Visual** button. This will export a `.pbiviz` (custom visual) file iand you can save this to your machine.

----
### Importing the Custom Visual

* In Power BI Desktop, click the ellipsis (...) in the *Visualizations* panel and select **Import from file**:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/powerbi-import-custom-visual.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Importing our Power BI custom visual</figcaption>
    </figure>
</div>

* If you're happy to proceed, confirm you wish to import
* In the dialog, locate your exported file and select **Open** (or double-click)
* If you see this in your panel, the visual will now be available to use:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/powerbi-custom-visual-added.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Our custom visual has been added</figcaption>
    </figure>
</div>

* Click on the visual to add it to your report canvas

----
### Adding Data Roles

If you've used the tool you may already have experienced this, but for those who havent, visuals created by charticulator need to work at the same data grain you designed them with in the tool.

Because our grain is one row per **DHB** per **Date**, we need to add both of these fields to the **Granularity (Level of Detail)** data role to ensure that our glyphs are resolved the same way. We'll then add our other fields and measures to the other data roled as normal. Mine looks like this:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/powerbi-data-roles.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Setting up the data roles for our custom visual</figcaption>
    </figure>
</div>

And... presto!

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/charticulator-sm/finished-recipe.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">All done!</figcaption>
    </figure>
</div>

----
# Assets and Materials

The above was quite a read! I'm hoping that if you've reached this you either want to compare your results to mine, or just have a look at the finished product before you start.

Here's copies of my work for this tutorial:

* [Charticulator definition (`.chart` file)](/assets/tutorial-downloads/charticulator-sm/NZ_DHB_Small_Multiples.chart)
* [Power BI custom visual (`.pbiviz` file)](/assets/tutorial-downloads/charticulator-sm/NZ_DHB_Small_Multiples.pbiviz)
* [Power BI workbook](/assets/tutorial-downloads/charticulator-sm/Charticulator_Small_Multiples_Example.pbix) - containing sample data and the custom visual already loaded

I hope that you've found this useful and it gives you some ideas on how to start creating your own no-code custom visuals ith small multiples. Good luck, and have fun!

DM-P