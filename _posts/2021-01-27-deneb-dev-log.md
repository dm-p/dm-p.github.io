---
layout: post
title: '"Deneb" Prototype Dev Log: 0.1.0.70'
author: daniel
categories: [Custom Visual, Prototype]
tags: [deneb, vega]
image: "assets/images/deneb/proto/70_splash.png"
description: 'Progress update for Jan 2021 on "Deneb", including some new changes that re moving towards improving integration with Power BI.'
featured: false
hidden: false
toc: false
permalink: deneb_build_70
---

---

For the uninitiated, "Deneb" is a prototyping exercise. The idea is to try and embed the <a href="https://vega.github.io/vega/" target="_blank">Vega</a>, and/or <a href="https://vega.github.io/vega-lite/" target="_blank">Vega-Lite</a> tooling into a Power BI visual with some editing tools. We can hopefully give creators a new way of building their own custom visuals within a Power BI workbook, using JSON to create specifications - in much the same way as <a href="https://vega.github.io/editor/" target="_blank">Vega Editor</a>, but with a dataset linked to the supplied columns and measures from the data model.

[Last update](/deneb_build_60) showed some decent progress, and I've certainly been busy over the last month or so. My initial goal of end-January from something "beta-able" has been hindered by having to stop and <a href="https://redux.js.org" target="_blank">learn Redux</a>, as well as a couple of work commitments. However, things have moved on sufficiently, that I think it's time to let anyone who is interested to see where we're at.

## Sample Data and Visual

For the purposes of illustration, we'll be working with a simpler visual than previous examples, so that we don't add reams of JSON to this post. We'll use a simple column and measure combination from our data model as follows:

![70_sample_data](/assets/images/deneb/proto/70_sample_data.png "A simple slice of our dataset, showing City and Mean Temperature.")

This is showing the _Mean Temperature_ for all values of _City_ in our data model. We'll use the visual to produce a simple bar chart, and because the bar chart is already a core visual, we'll do something weird to ours that the core visual can't do, like give it rounded corners:

![70_simple_bar_chart](/assets/images/deneb/proto/70_simple_bar_chart.png "A simple bar chart with rounded corners, generated using "Deneb".")

As we can see, we've added the _City_ column and _Mean Temperature_ measure to the visual's **Values** field, much like we would do for a table, R or Python visual.

And how did we actually build the bar chart? Well, in the Visual Header we click on the ellipsis and choose _Edit_ to open the Visual Editor, like so:

<img src="/assets/images/deneb/proto/70_advanced_editor.gif" width="800" title="Opening the Visual Editor from the visual header.">

We'll cover the how's a bit more as we continue on with the post.

## Revised Interface

First-up, the majority of the Visual Editor's interface has been re-worked, and (mostly) implemented using <a href="https://developer.microsoft.com/en-us/fluentui#/" target="_blank">Fluent UI</a>, for a more in-keeping look.

The icons at the top will:

-   _Apply Changes_
-   _Auto-Apply Changes_ (as you type)
-   _Discard_ the visual's current Spec and Config
-   Provide help (TODO: write help)

It's likely there will be a few more icons added to this part of the UI as some more operations get folded-in.

The panes underneath will provide access to...

#### 1. The Spec Editor

This allows us to write our visual spec, which is in JSON. We have the following in our example, which will create the simple bar:

![70_spec_editor](/assets/images/deneb/proto/70_spec_editor.png "The Spec editor, showing the JSON required to build our visual.")

This is a simple Vega-Lite specification. To access the visual's data, we declare a <a href="https://vega.github.io/vega-lite/docs/data.html#named" target="_blank">named data source</a> called `"values"`. This will map any columns and measures by the name supplied in the **Values** field, and we can see that the `"x"` and `"y"` encodings have these references, and this will bind the data accordingly.

#### 2. The Config Editor

The Config editor allows you to seperate out your top-level configuration of the visual view from the Spec, should you wish to do so. The Spec and Config are merged together when changes are applied to create the full view. Here's what this looks like for our example:

![70_config_editor](/assets/images/deneb/proto/70_config_editor.png "The Config editor, showing the JSON we're applied to our visual's top-level configuration.")

Here, we've specified `autosize` properties, <a href="https://vega.github.io/vega-lite/docs/size.html#autosize" target="_blank">which we can use to automatically size to our visual container</a> and saves us a lot of work for simple visuals that use a single or layered view. More complex plots will require specific height and width info.

We also have applied the radius properties to our `"bar"` mark, so that the top-right and bottom-right corners are rounded.

> Note that you can still add a `"config"` property to the _Spec_ and use that if you want to keep everything in one place.

#### 3. Visual Settings

This tab allows you to configure a couple of Vega-specific options:

![70_settings_pt1](/assets/images/deneb/proto/70_settings_pt1.png "The Settings pane, which allows us to configure some behaviour for Vega.")

Here, we're able to specify whether we want to use Vega-Lite or Vega for our spec format. The Spec editor will validate your JSON accordingly and render the visual using the selected provider.

We can also specify whether the Vega tools render using SVG or Canvas.

## One More Thing...

For the last couple of posts, I've been toeing this particular line:

> **To manage your expectations:** because this tooling is so open-ended it is highly likely that we will not be able to interact back with other visuals. Cross-filtering, highlighting, drillthrough, report page tooltips etc. have to be implemented with a very fixed idea of what the data structure inside a visual looks like, so that Power BI can perform the necessary lookups between its data points and those of other visuals in a report. As such, the cost of flexible design and implementation currently has some limitations, but I will see if I can do anything about this further down the track; certainly not for a v1 of the visual though.

Well, the Settings pane in build 70 also has this additional section:

![70_settings_pt2](/assets/images/deneb/proto/70_settings_pt2.png "The part of the Settings pane I'm really excited about.")

Enabling these will allow you to utilise that particular interactivity function of Power BI, **with some caveats**:

-   The Vega and Vega-Lite toolting is incredibly configurable, so there are going to be many use cases that don't work, because I'm not able to test them all. We will need to look at these on a case-by-case basis and see how we can potentially support them.

-   I'm doing as much as I can behind the scenes when your view is generated but due to the flexible design potential, there might be some additional setup required in your spec. This is due to how the Vega tooling works, and what you might want to do as a creator (I will highlight these requirements in each section accordingly).

-   Any mark that you wish to apply interactivity to needs to resolve to a "pure" data point. This means that if you apply any <a href="https://vega.github.io/vega-lite/docs/transform.html" target="_blank">transformations</a> that mutate the data point, we can no longer tie it back to the corresponding context in the data model.

While this is not 100% in-line with core visuals, it should certainly provide much more flexibility over R and Python visuals when it comes to building a bespoke design.

#### Tooltip Handler

Enabling this setting will load a purpose-built tooltip handler that overrides the Vega default and will attempt to reconcile data points back to Power BI.

You will need to enable a mark for tooltips for Vega/Vega-Lite to display the tooltip on hover. For our Vega-Lite example, we can do the following in our config (or spec):

![70_tooltip_config](/assets/images/deneb/proto/70_tooltip_config.png "Enabling a mark for tooltip in a Vega-Lite specification.")

For a Vega specification, we can add the following to a mark's `"enter"` encoding:

```json
"tooltip": {"signal": "datum"}
```

When the changes have been applied, we can hover over any enabled mark and we'll get a Power BI tooltip, e.g.:

![70_tooltip_enabled](/assets/images/deneb/proto/70_tooltip_enabled.png "Hovering over a tooltip-enabled mark will display a Power BI tooltip.")

Now for the cool part - if you've built a tooltip page with any of the columns in your dataset (and provided the data point is "pure"), the handler will be able to resolve it, e.g.:

![70_tooltip_page](/assets/images/deneb/proto/70_tooltip_page.png "If a suitable tooltip page is set-up and your dataset contains an applicable field then we can resolve it.")

If you have transformed a data point in the specification and enabled it for tooltips, the visual will at least display a standard tooltip of the transformed values.

#### Context Menu

Enabling this setting will display the standard Power BI context menu when you right-click a data point. If the point is "pure" and a suitable drillthrough page exists for one of the columns in the dataset, then you are able to drill-through, e.g.:

![70_context_drillthrough](/assets/images/deneb/proto/70_context_drillthrough.png "If a suitable drillthrough page is set-up and your dataset contains an applicable field then we can allow drillthrough from a mark.")

For Vega-Lite specifications, this currently only works for those containing a single view. I'll be working to sort this out for more complicated specifications, as this is the primary use case for the visual. Vega specifications tested so far all seem to be okay.

#### Selection

Enabling this setting will enable a data point for selection (providing it is "pure"). Clicking, or Ctrl + clicking data points will cross-filter other visuals, e.g.:

![70_selection_enabled](/assets/images/deneb/proto/70_selection_enabled.gif "Enabling selection will allow clicking a "pure" data point to filter other visuals.")

Note that our example isn't providing any visual cues, and this is because I can't predict what you as a creator want to do here. So, you can configure your mark's encoding to leverage a special selection named `__select__` as needed, e.g.:

![70_adding_selection_encoding](/assets/images/deneb/proto/70_adding_selection_encoding.png "We need to declare in our spec, how selections should be represented visually.")

Here, we've added an encoding on our mark's `"opacity"`, which will ensure it's `1` (fully opaque) if selected, or `0.3` (30% opaque) if not. Now, we can take another go at this, e.g.:

![70_selection_opacity](/assets/images/deneb/proto/70_selection_opacity.gif "Once we have set up an appropriate visual cue in our encoding, things make a bit more sense.")

Selection state for eligible data points can also work with bookmarks, e.g.:

![70_selection_bookmarks](/assets/images/deneb/proto/70_selection_bookmarks.gif "Once we have set up an appropriate visual cue in our encoding, things make a bit more sense.")

The selection functionality is very much a "from tiny acorns..." type of feature. Much like the context menu, this only supports a single Vega-Lite view at present, but I'll be looking to see how I can extend this to more complex plots, as similarly this is where we would want to try and leverage this kind of functionality if possible. There are still a few UX gremlins as well, but I'm pretty happy that things are moving in the right direction 😊

## Want to Have a Look?

Here's a sample workbook with a slightly more customised version of our sample bar chart, and is probably best being played with in full-screen view. You can try interacting with it to see how things are going - there's a tooltip page and a drillthrough page, and some bookmarks. The drillthrough page also has the latest version of another chart that has appeared in previous posts, and is also built using this visual.

---

<iframe width="1024" height="612" src="https://app.powerbi.com/view?r=eyJrIjoiZjk1MzlmM2YtNzY2NC00Y2U2LTgwZmQtZWZkMjgxNmQ5YjBhIiwidCI6IjUzYmJlMGQ3LTU0NzItNGQ0NS04NGY0LWJiNzJiYjFjMjI4OSJ9" frameborder="0" allowFullScreen="true"></iframe>

---

I'm planning to focus more on adding the remaining features over the coming few weeks, so hopefully we can get this out to a wider audience for beta-testing very soon.

Thanks for reading! Until next time,

DM-P
