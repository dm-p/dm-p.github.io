---
layout: post
title: '"Out of One, Many", or: Making Switchable Charts in Power BI with Deneb'
author: daniel
categories: [Power BI, Custom Visual, Hacks]
tags: [deneb, vega-lite]
image: "assets/images/deneb/chart-switcher/chart-switcher-post.png"
description: "When Personalize Visuals is too personalized: an experiment in switching displayed charts using a dimension attribute."
featured: false
hidden: false
permalink: deneb-chart-switcher
---

As many of you may know, when I'm not tinkering with Power BI visuals on my own time, I do a lot of work with <a href="https://www.discoverei.com/" target="_blank">DiscoverEI</a>. A common scenario for our customers can involve visualising a wide range of <a href="https://en.wikipedia.org/wiki/Analyte" terget="_blank">analytes</a> for their environmental reports.

Our design goals are to make the processes as straightforward as possible for users, so we typically model our reports using fact tables that include the analytes of interest as part of their granularity, and then have a dimension table for the analyte details, which makes slicing and switching really easy for report viewers.

What's often quite complicated with this process is that not all analytes are created equal, even though the overall 'shape' of the data is the same:

- Some have thresholds, in order to be able to check whether they're within safe limits;
- Some use multiple measures;
- Some need to be visualised differently to others (possibly due to combination of the above or even sometimes down to industry standards or stakeholder preferences).

## What We'd Love to Do

So, for these situations, what would be like to be able to do? The idea of data-driven chart selection sounds pretty cool:

- By selecting a particular analyte in a slicer...
- ...the main chart that we're using could change into a more optimal one...
- ...so that it provides the ideal way of visualising our particular analyte.

## What We Currently Have

But really, how can that work in Power BI currently? We have the following options (that I'm currently aware of):

- <a href="https://docs.microsoft.com/en-us/power-bi/create-reports/desktop-bookmarks" target="_blank">Bookmarks</a> - we could provide bookmarks for each type of chart and allow a user to trigger this, but still relies on them to manually choose the bookmark to view the "correct" chart.
- <a href="https://docs.microsoft.com/en-us/power-bi/create-reports/power-bi-personalize-visuals" target="_blank">Personalize Visuals</a> - provide the means for a user to customise the visual, by swapping out the chart and fields themselves. Again, very powerful but if there are specific requirements around how the chart should be displayed, then this still requires manual intervention for our case.
- <a href="https://www.sqlbi.com/calculation-groups/" target="_blank">Calculation groups</a> and <a href="https://docs.microsoft.com/en-us/power-bi/create-reports/power-bi-field-parameters" target="_blank">fields parameters</a> - very flexible and heaps of resources out there already on how to leverage them, but still relies on the same base chart (just swaps the data available). It's probably the closest we can get out of the three options here, but still isn't right for our case.

Until recently, we've had to work with these options, but in a chat with <a href="https://twitter.com/AliceDiscoverEI" target="_blank">Alice Drummond</a> the other day, I started to wonder if Deneb could manage this in the right set of circumstances.

## Defining the Challenge

For this case, I'm going to keep things simple(!) by working with the following scenario:

- I have some data for New Zealand that records average temperature by day over many years for many different locations.
- (Fictional) location owners have a preference on how their data should be viewed when their location is selected using a slicer. This could be one of three types of visual:
  - Box Plot
  - Density Plot
  - Heatmap

#### Managing Location Preferences

Their preferences would look something like the following (although there are many more locations in the model):

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/chart-switcher/location-preference-sample-data.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">A graphic of a table, showing some New Zealand locations, and the preference of chart that each owner would like when their location is selected in the report slicer. Chart Types are one of, <i>"Box Plot"</i>, <i>"Density"</i> or <i>"Heatmap"</i></figcaption>
    </figure>
</div>

These are added to the table that manages _Location_, as an attribute (column).

#### Data Model

Our data model currently looks like this:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/chart-switcher/model.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Our simple data model: dimensions for Date and Location, with a fact table for our Daily Temperature Readings</figcaption>
    </figure>
</div>

#### The Results

On our page, we have a single select slicer with our _Location_, and we've created a separate visual using Deneb with the following dataset:

- _Year Month_ - date field, representing the start date of each month;
- _Average Temperature_ - our measure, aggregating the average temperature reading at our monthly level of grain;
- _Chart Type_ - our column representing "preference" from the _Location_ table.

I've then written a Vega-Lite specification, which will look at the _Chart Type_ and plot the chart according to this value. The video below illustrates the basic concept working within Power BI:

<div style="text-align: center;">
<video controls width="80%">
<source src="/assets/mp4/deneb/chart-switcher/simple-chart-switcher-example.mp4" type="video/mp4">
Sorry, your browser doesn't support embedded videos.
</video>
</div>

<br/>
This achieves the desired effect of a data-driven chart using the same visual container and no intervention from the end-user other than changing a slicer.

## How It's Done

The short answer is: it's done using the <a href="https://vega.github.io/vega-lite/docs/layer.html" target="_blank">layer composition in Vega-Lite</a>. However, due to how layers, axes and scales are unioned in Vega-Lite, it's a bit more complicated in practice.

I'll break down my approach in the following sections.

### The Individual Charts

To begin with, let's start with breaking the three types of chart we need into their own simple Vega-Lite specifications:

#### Box Plot

Our box plot is quite straightforward, using the `boxplot` mark and breaking this out by the `"timeUnit": "month"` component of the _Year Month_ field, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/chart-switcher/boxplot-initial.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">A box plot of <i>Average Temperature</i> for each month of the year.</figcaption>
    </figure>
</div>

The Vega-Lite specification for this is as follows:

```json
{
  "data": { "name": "dataset" },
  "mark": { "type": "boxplot" },
  "encoding": {
    "x": {
      "field": "Year Month",
      "timeUnit": "month",
      "type": "ordinal",
      "axis": {
        "labelAngle": 0,
        "format": "%b"
      }
    },
    "y": {
      "field": "Average Temperature",
      "type": "quantitative"
    }
  }
}
```

#### Density Plot

The density plot can be reproduced using Vega-Lite with a `density` transform:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/chart-switcher/density-plot-initial.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">A density plot of <i>Average Temperature</i> across the entire data set.</figcaption>
    </figure>
</div>

And, here's our specification for this one:

```json
{
  "data": { "name": "dataset" },
  "transform": [
    {
      "density": "Average Temperature",
      "bandwidth": 0.4
    }
  ],
  "mark": { "type": "area" },
  "encoding": {
    "x": {
      "field": "value",
      "type": "quantitative"
    },
    "y": {
      "field": "density",
      "type": "quantitative",
      "axis": {
        "format": "0.1%"
      }
    }
  }
}
```

#### Heatmap

The heatmap can also be produced with minimal effort in Vega-Lite:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/chart-switcher/heatmap-initial.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">A heatmap of <i>Average Temperature</i> with, <i>Month</i> on the y-axis, and <i>Year</i> on the x-axis.</figcaption>
    </figure>
</div>

Here's the Vega-Lite specification for this one, too:

```json
{
  "data": { "name": "dataset" },
  "mark": { "type": "rect" },
  "encoding": {
    "x": {
      "field": "Year Month",
      "timeUnit": "year",
      "type": "quantitative"
    },
    "y": {
      "field": "Year Month",
      "timeUnit": "month",
      "type": "ordinal"
    },
    "color": {
      "field": "Average Temperature",
      "type": "quantitative",
      "legend": null
    }
  }
}
```

### The Conventional Approach

If we're going to use layers conventionally, then we're going to run into problems. What I mean, is that if we approach it like this:

```json
{
  "data": { "name": "dataset" },
  "layer": [
    { boxplot },
    { density },
    { heatmap }
  ]
}
```

...we're going to get some funky results, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/chart-switcher/layering-all-initial-charts.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">If we compose all our charts into layers then we get Vega-Lite's best attempt to resolve everything.</figcaption>
    </figure>
</div>

To be fair, we've given Vega-Lite a lot of things to resolve and it's done its best to make it work. Each chart has a different x and y axis to the other, and we can see bits of the other charts poking out from behind the heatmap. This isn't readable and doesn't achieve our objective.

<details>
<summary>(by the way, if you want to see the full specification for this step, rather than the condensed version above, you can expand this section)</summary>
<pre>
{
  "data": {"name": "dataset"},
  "layer": [
    {
      "mark": {"type": "boxplot"},
      "encoding": {
        "x": {
          "field": "Year Month",
          "timeUnit": "month",
          "type": "ordinal",
          "axis": {
            "labelAngle": 0,
            "format": "%b"
          }
        },
        "y": {
          "field": "Average Temperature",
          "type": "quantitative"
        }
      }
    },
    {
      "transform": [
        {
          "density": "Average Temperature",
          "bandwidth": 0.4
        }
      ],
      "mark": {"type": "area"},
      "encoding": {
        "x": {
          "field": "value",
          "type": "quantitative"
        },
        "y": {
          "field": "density",
          "type": "quantitative",
          "axis": {"format": "0.1%"}
        }
      }
    },
    {
      "mark": {"type": "rect"},
      "encoding": {
        "x": {
          "field": "Year Month",
          "timeUnit": "year",
          "type": "quantitative"
        },
        "y": {
          "field": "Year Month",
          "timeUnit": "month",
          "type": "ordinal"
        },
        "color": {
          "field": "Average Temperature",
          "type": "quantitative",
          "legend": null
        }
      }
    }
  ]
}</pre>
</details>

---

### The Revised Approach

Hey, didn't we have a _Chart Type_ field that we added to our dataset that's supposed to make this work?

Yes, we did! And now we'll start to leverage it.

#### Filtering Layers

What we _can_ do is ensure that our layers are only rendered if there's actually data to plot, and we can use a <a href="https://vega.github.io/vega-lite/docs/filter.html" target="_blank">filter transform</a> on each layer to force this.

What I'm also going to do (because the _Chart Type_ is the same for all rows) is extract the first row's value into a <a href="https://vega.github.io/vega-lite/docs/parameter.html" target="_blank">parameter</a> that can be used at a global level. I'm going to call this `chart_type_value`.

> Note that you could bind a parameter directly inside the visual if you wanted to provide user input, but this doesn't achieve our objective in this scenario.

Our strategy now looks as follows:

```json
{
  "data": { "name": "dataset" },
    "params": [
    {
      "name": "chart_type_value",
      "expr": "data('dataset')[0]['Chart Type']"
    }
  ],
  "layer": [
    {
      "transform": [
        {
          "filter": "chart_type_value == 'Box Plot'"
        }
      ],
      ...
    },
    {
      "transform": [
        {
          "filter": "chart_type_value == 'Density'"
        }
      ],
      ...
    },
    {
      "transform": [
        {
          "filter": "chart_type_value == 'Heatmap'"
        }
      ],
      ...
    }
  ]
}
```

In the case of the density layer, this already has a `transform` property; I've removed this to illustrate the `filter` approach that we need to apply.

<details>
<summary>(you can expand this section to see the fully fleshed-out specification for this step)</summary>
<pre>
{
  "data": {"name": "dataset"},
  "params": [
    {
      "name": "chart_type_value",
      "expr": "data('dataset')[0]['Chart Type']"
    }
  ],
  "layer": [
    {
      "transform": [
        {
          "filter": "chart_type_value == 'Boxplot'"
        }
      ],
      "mark": {"type": "boxplot"},
      "encoding": {
        "x": {
          "field": "Year Month",
          "timeUnit": "month",
          "type": "ordinal",
          "axis": {
            "labelAngle": 0,
            "format": "%b"
          }
        },
        "y": {
          "field": "Average Temperature",
          "type": "quantitative"
        }
      }
    },
    {
      "transform": [
        {
          "filter": "chart_type_value == 'Density'"
        },
        {
          "density": "Average Temperature",
          "bandwidth": 0.4
        }
      ],
      "mark": {"type": "area"},
      "encoding": {
        "x": {
          "field": "value",
          "type": "quantitative"
        },
        "y": {
          "field": "density",
          "type": "quantitative",
          "axis": {"format": "0.1%"}
        }
      }
    },
    {
      "transform": [
        {
          "filter": "chart_type_value == 'Heatmap'"
        }
      ],
      "mark": {"type": "rect"},
      "encoding": {
        "x": {
          "field": "Year Month",
          "timeUnit": "year",
          "type": "quantitative"
        },
        "y": {
          "field": "Year Month",
          "timeUnit": "month",
          "type": "ordinal"
        },
        "color": {
          "field": "Average Temperature",
          "type": "quantitative",
          "legend": null
        }
      }
    }
  ]
}</pre>
</details>

---

Our visual is getting closer to what we want; it will now show the desired chart for the slicer selection, but it still looks a bit messy around the axes, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/chart-switcher/chart-after-filtering.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">After applying a filter transform to each layer for the <i>Chart Type</i> we expect, we only get the intended marks plotted that we want. However, the axes still don't look right and all labels and titles are shown for all layers, even though there's no data.</figcaption>
    </figure>
</div>

Here, we can still see that even though the other layers aren't being plotted, Vega-Lite is still reserving the scales and axes for them. This is where we need to get a bit more creative with how our scales are defined and how our axes are drawn.

#### Fixing the Scales and Axes

If we were writing a custom visual using the visuals SDK and TypeScript, this kind of thing is easy to manage because our visual's logic could only supply the things we need to the specification. However, as Deneb works with a single specification that needs to accommodate all use cases, then we have to game the system a bit.

We do this by prescribing the following behaviours more explicitly in each layer:

- Using `resolve` for the `x` and `y` scales;
- Using `orient` to explicitly position axes where we want them;
- Conditionally formatting axis labels with `labelExpr`;
- Either hiding the axis titles by setting them to `null`, or conditionally setting them using a similar strategy as the labels.

I'm going to deal with each of these in isolation and rather than show the whole specification at each stage, I'll lay this out fully at the end.

##### Scale Resolution

This will vary based on the data you're plotting, but if you see the individual charts not using all available space, we may need to correct the behaviour using this approach.

As we're only plotting a single chart at a time, we don't need to worry about uniform scales. We'll set the `x` and `y` channels to <a href="https://vega.github.io/vega-lite/docs/resolve.html" target="_blank">resolve independently</a> in the top-level of our specification, e.g.:

```json
{
  ...
  "resolve": {
    "scale": {
      "x": "independent",
      "y": "independent"
    }
  },
  ...
}
```

##### Axis Orientation

As we've observed, Vega-Lite attempts to use the top and right of our visual for additional axes. Normally this would be what we want, but because we're attempting to make our 'preferred' chart appear normally, then we want to <a href="https://vega.github.io/vega-lite/docs/axis.html#general" target="_blank">explicitly position them</a> using the `orient` property.

Some of our base charts already have an `"axis"` object in their `"encoding"` channels, and we can add to these. For the others, we will need to explicitly add them. Based on the example we're building here, the `"encoding"` channels will look as follows:

###### Box Plot

```json
      "encoding": {
        "x": {
          "field": "Year Month",
          "timeUnit": "month",
          "type": "ordinal",
          "axis": {
            "labelAngle": 0,
            "format": "%b",
            "orient": "bottom"
          }
        },
        "y": {
          "field": "Average Temperature",
          "type": "quantitative",
          "axis": {
            "orient": "left"
          }
        }
      }
```

###### Density Plot

```json
      "encoding": {
        "x": {
          "field": "value",
          "type": "quantitative",
          "axis": {"orient": "bottom"}
        },
        "y": {
          "field": "density",
          "type": "quantitative",
          "axis": {
            "format": "0.1%",
            "orient": "left"
          }
        }
      }
```

###### Heatmap

```json
      "encoding": {
        "x": {
          "field": "Year Month",
          "timeUnit": "year",
          "type": "quantitative",
          "axis": {
            "orient": "bottom"
          }
        },
        "y": {
          "field": "Year Month",
          "timeUnit": "month",
          "type": "ordinal",
          "axis": {
            "orient": "left"
          }
        },
        "color": {
          "field": "Average Temperature",
          "type": "quantitative",
          "legend": null
        }
      }
```

---

With these explicitly set, our axes are positioned correctly. They still look weird, but they're in the right place:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/chart-switcher/chart-after-orient.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Once we explicitly set the encoding for each axis to orient in the specific location, our axes are positioned where we expect. We still have some issues with the labels and titles, and these need to be manually managed.</figcaption>
    </figure>
</div>

We'll fix the labels next.

##### Conditionally Showing the Labels

With axis labels, we can use the `labelExpr` property to use <a href="https://vega.github.io/vega/docs/expressions/" target="_blank">Vega's expression language</a> to apply conditional formatting.

<a href="https://vega.github.io/vega-lite/docs/axis.html#general:~:text=regular%20color%20name.-,labelExpr,-String" target="_blank">In the case of axis labels</a>, we don't have access to our data rows, as the `datum` that is exposed here is at the label level. However, because we exposed the _Chart Type_ value via a parameter that is global to the specification (`chart_type_value`), then we _can_ access this in the label scope 🙂.

For each label, we need to set up a simple <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator" target="_blank">ternary expression</a> (which is the same as an if/else) to show the labels if our chart type matches the selected one.

For example, the `labelExpr` for each axis in the box plot would look as follows:

```
    "labelExpr": "chart_type_value == 'Boxplot' ? datum.label : null"
```

And once these changes have been propagated to the other axes in the other layers (remembering to use the correct expected value for the `chart_type_value`!) then only the correct labels will be shown in-context.

##### Fixing the Titles

This section has two possible approaches: the officially supported one and one that works, but probably shouldn't (although <a href="https://github.com/vega/vega-lite/issues/7264" target="_blank">there is an issue</a> in Vega-Lite's backlog to implement correctly).

We can therefore either set the axis title to `null` in all `axis` objects in each of the layers, and this will hide all titles, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/chart-switcher/axis-null-titles.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">By explicitly setting all axis title properties to <code>null</code>, Vega-Lite will not display them, effectively cleaning-up our visual.</figcaption>
    </figure>
</div>

What would be neat is if we could set an `expr` on the `title` property. I would note that this _sort of_ works, but not for all mark types. For instance, having the box plot in the spec will cause some unintended behaviour, but could work well if you're using non-composite marks.

My alternative approach was to create a measure that could be used for the title, and be set based on the current _Chart Type_ value, e.g.:

<pre>
Chart&nbsp;Type&nbsp;Title&nbsp;=<br><span class="Keyword" style="color:#035aca">VAR</span>&nbsp;<span class="Variable" style="color:#49b0af">ChartType</span>&nbsp;=<br><span class="indent4">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Keyword" style="color:#035aca">MAX</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span>&nbsp;Location[Chart&nbsp;Type]&nbsp;<span class="Parenthesis" style="color:#808080">)</span><br><span class="Keyword" style="color:#035aca">RETURN</span><br><span class="indent4">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Keyword" style="color:#035aca">SWITCH</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span><br><span class="indent8">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Keyword" style="color:#035aca">TRUE</span><span class="Parenthesis" style="color:#808080">&nbsp;(</span><span class="Parenthesis" style="color:#808080">)</span>,<br><span class="indent8">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Variable" style="color:#49b0af">ChartType</span>&nbsp;=&nbsp;<span class="StringLiteral" style="color:#D93124">"Heatmap"</span>,&nbsp;<span class="StringLiteral" style="color:#D93124">"Heatmap&nbsp;of&nbsp;Average&nbsp;Temperature&nbsp;(°C)&nbsp;by&nbsp;Month&nbsp;and&nbsp;Year"</span>,<br><span class="indent8">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Variable" style="color:#49b0af">ChartType</span>&nbsp;=&nbsp;<span class="StringLiteral" style="color:#D93124">"Density"</span>,&nbsp;<span class="StringLiteral" style="color:#D93124">"Density&nbsp;of&nbsp;Average&nbsp;Temperature&nbsp;distribution&nbsp;(°C)"</span>,<br><span class="indent8">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Variable" style="color:#49b0af">ChartType</span>&nbsp;=&nbsp;<span class="StringLiteral" style="color:#D93124">"Box&nbsp;Plot"</span>,&nbsp;<span class="StringLiteral" style="color:#D93124">"Average&nbsp;Temperature&nbsp;(°C)&nbsp;distribution&nbsp;by&nbsp;Month"</span>,<br><span class="indent8">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="StringLiteral" style="color:#D93124">"No&nbsp;chart&nbsp;preferences&nbsp;specified"</span><br><span class="indent4">&nbsp;&nbsp;&nbsp;&nbsp;</span><span class="Parenthesis" style="color:#808080">)</span><br>
</pre>

And here's a short video of how this looks in-situ:

<div style="text-align: center;">
<video controls width="80%">
<source src="/assets/mp4/deneb/chart-switcher/simple-chart-switcher-dynamic-title.mp4" type="video/mp4">
Sorry, your browser doesn't support embedded videos.
</video>
</div>
<br/>

## Complete Specification

If you want to have a play with this, I've <a href="https://community.powerbi.com/t5/Data-Stories-Gallery/Out-of-One-Many-or-Making-Switchable-Charts-in-Power-BI-with/td-p/2588205" target="_blank">added this to the Data Stories Gallery</a> on the Power BI Community forums.

For anyone keen to explore and adapt, here's the full specification from this post:

```json
{
  "data": { "name": "dataset" },
  "params": [
    {
      "name": "chart_type_value",
      "expr": "data('dataset')[0]['Chart Type']"
    }
  ],
  "resolve": {
    "scale": {
      "x": "independent",
      "y": "independent"
    }
  },
  "layer": [
    {
      "description": "Box plot",
      "transform": [
        {
          "filter": "chart_type_value == 'Box Plot'"
        }
      ],
      "mark": { "type": "boxplot" },
      "encoding": {
        "x": {
          "field": "Year Month",
          "timeUnit": "month",
          "type": "ordinal",
          "axis": {
            "title": null,
            "labelAngle": 0,
            "labelExpr": "chart_type_value == 'Box Plot' ? datum.label : ''",
            "format": "%b",
            "orient": "bottom"
          }
        },
        "y": {
          "field": "Average Temperature",
          "type": "quantitative",
          "axis": {
            "title": null,
            "labelExpr": "chart_type_value == 'Box Plot' ? datum.label : ''",
            "orient": "left"
          }
        }
      }
    },
    {
      "description": "Density plot",
      "params": [
        {
          "name": "layer_chart_type",
          "value": "Density"
        }
      ],
      "transform": [
        {
          "filter": "chart_type_value == 'Density'"
        },
        {
          "density": "Average Temperature",
          "bandwidth": 0.4
        }
      ],
      "mark": { "type": "area" },
      "encoding": {
        "x": {
          "field": "value",
          "type": "quantitative",
          "axis": {
            "title": null,
            "labelExpr": "chart_type_value == 'Density' ? datum.label : ''",
            "orient": "bottom"
          }
        },
        "y": {
          "field": "density",
          "type": "quantitative",
          "axis": {
            "title": null,
            "labelExpr": "chart_type_value == 'Density' ? datum.label : ''",
            "format": "0.1%",
            "orient": "left"
          }
        }
      }
    },
    {
      "description": "Heatmap chart",
      "transform": [
        {
          "filter": "chart_type_value == 'Heatmap'"
        }
      ],
      "mark": { "type": "rect" },
      "encoding": {
        "x": {
          "field": "Year Month",
          "timeUnit": "year",
          "type": "quantitative",
          "axis": {
            "title": null,
            "labelExpr": "chart_type_value == 'Heatmap' ? datum.label : ''",
            "orient": "bottom"
          }
        },
        "y": {
          "field": "Year Month",
          "timeUnit": "month",
          "type": "ordinal",
          "axis": {
            "title": null,
            "labelExpr": "chart_type_value == 'Heatmap' ? datum.label : ''",
            "orient": "left"
          }
        },
        "color": {
          "field": "Average Temperature",
          "type": "quantitative",
          "legend": null
        }
      }
    }
  ]
}
```

---

## Wrapping-Up

Whilst this has taken us a while to get there, I'm hoping that you've found the experiment worthwile reading. It's certainly been an interesting challenge to approach and I'm definitely going to explore this a bit more. It could certainly be interesting to see if we could make this easier in Deneb if this is a case that other people find useful. If anyone has any ideas, <a href="https://github.com/deneb-viz/deneb/issues" target="_blank">please feel free to create an issue for it</a> and we can try and work through how it could potentially work as a feature.

I'm quite excited to see if smarter folks than me can work out if there's better ways to manage some of the shortcomings I've worked around, or if there are more efficient ways to solve this within a single specification. If you have anything you've found, please let me know - I'd love to see how this idea can be evolved!

Thanks for reading,

DM-P
