---
layout: post
title: "Deneb Now Available for Public Preview"
author: daniel
categories: [Custom Visual, Preview]
tags: [deneb, vega]
image: "assets/images/deneb/preview/preview_banner.png"
description: "Here goes..."
featured: true
hidden: true
toc: false
permalink: deneb_public_preview
---

It's been a pretty awesome month for folk who want more visualisation opportunities in Power BI - we've seen the launch of <a href="https://pureviz.net/infographic" target="_blank">PureViz Infographic</a>, and last week <a href="https://powerbi.microsoft.com/en-us/blog/announcing-the-new-charticulator-visual-public-preview/" target="_blank">Microsoft announced the Charticulator visual</a>.

If that's not enough to be getting on with, a short while back [I mentioned I've been working on the idea](/viz_202012#deneb---visualisation-grammar-prototype) of bringing the <a href="https://vega.github.io/vega/" target="_blank">Vega</a> and <a href="https://vega.github.io/vega-lite" target="_blank">Vega-Lite</a> languages into Power BI. For anyone who wants to get started, it's now available for public preview 😀

**The latest version of visual can be downloaded at <a href="https://deneb-viz.github.io/" target="_blank">deneb-viz.github.io</a>, along with supporting documentation for current functionality.**

I'm doing an intro session this week at the <a href="https://globalpowerbisummit.com/" target="_blank">Power BI Summit</a> for anyone who is attending and would like to know a bit more, but for those that aren't able to attend, I'll provide a short overview below.

## A Little Bit About Vega

To borrow the high-level summary from <a href="https://vega.github.io/vega/" target="_blank">Vega</a>'s website:

> Vega is a visualization grammar, a declarative language for creating, saving, and sharing interactive visualization designs. With Vega, you can describe the visual appearance and interactive behavior of a visualization in a JSON format, and generate web-based views using Canvas or SVG.

<div class="text-center">
    <figure class="figure">
        <img width="1000" src="/assets/images/deneb/preview/vega_examples.png" class="figure-img img-fluid rounded" title="Some examples of Vega output, taken from Vega's site">
    </figure>
    <figcaption class="figure-caption">There's a <a href="https://vega.github.io/vega/examples/" target="_blank">great set of examples on Vega's site</a> that demonstrates the kinds of things that are possible.</figcaption>
</div>

<br/>
And if Vega seems a little too high a barrier to entry, there's also <a href="https://vega.github.io/vega-lite" target="_blank">Vega-Lite</a>, which is easier to get into, but still very powerful in terms of producing good quality visualisations:

> Vega-Lite is a high-level grammar of interactive graphics. It provides a concise, declarative JSON syntax to create an expressive range of visualizations for data analysis and presentation.

<div class="text-center">
    <figure class="figure">
        <img width="1000" src="/assets/images/deneb/preview/vega-lite_examples.png" class="figure-img img-fluid rounded" title="Some examples of Vega-Lite's output, taken from Vega-Lite's site">
    </figure>
    <figcaption class="figure-caption">Similarly, you can <a href="https://vega.github.io/vega-lite/examples/" target="_blank">check out the Vega-Lite examples page</a> to see what it can do.</figcaption>
</div>

<br/>
I would recommend Vega-Lite to begin with, and it's the default language for new instances of a Deneb visual.

Both languages are from the work at the <a href="https://idl.cs.washington.edu/" target="_blank">UW Interactive Data Lab</a>, and it's many alumni and contributors who continue to evolve it. It's built for the web, and therefore a great fit for a tool like Power BI, without the need for inventing another proprietary language.

This flips the "Power BI way" of doing things on its head: rather than picking a pre-defined chart and working with only the options available via the properties pane to customise it, you can instead work with the data you want and design a visual from the ground up. You can then iterate your design as you explore your insights. If you're developed visuals in Power BI using <a href="https://docs.microsoft.com/en-us/power-bi/create-reports/desktop-r-visuals?WT.mc_id=DP-MVP-5003712" target="_blank">R</a> and/or <a href="https://docs.microsoft.com/en-us/power-bi/connect-data/desktop-python-visuals?WT.mc_id=DP-MVP-5003712">Python</a>, you may have used libraries like <a href="https://ggplot2.tidyverse.org/" target="_blank">ggplot2</a>, <a href="https://plotly.com/" target="_blank">plotly</a>, <a href="https://seaborn.pydata.org/" target="_blank">seaborn</a> or others to a similar effect.

## What Deneb Does

-   **Packages-up both Vega and Vega-Lite parsers and runtimes**. You just need to specify which language you're using <a href="https://deneb-viz.github.io/visual-editor#settings-tab" target="_blank">in the settings tab</a>. There's also no external dependencies, like there are for R and Python visuals, so if your visual works for you, it should work exactly the same for all your users.
-   **Binds columns or measures you add to the visual as a named dataset**. For consistency with the R and Python visuals, this is also named `dataset`. You can read more about the inner-workings of that part <a href="https://deneb-viz.github.io/visual-dataset-binding" target="_blank">on Deneb's site</a>.
-   **Provides an editing environment for a JSON specification**. There's an editor for the main specification, and another for the top-level configuration (which allows you to abstract some of the cosmetic elements of your work).
-   **Provides (some) interactivity bindings with Power BI**. And hopefully more later on. Right now, <a href="https://deneb-viz.github.io/interactivity-tooltips" target="_blank">we have tooltips working</a>. Under the right conditions, report page tooltips work as well.
-   **Provides Some In-Built Examples** so that you can have a go at some simple visualisations straight away. You'll be able to map columns and measures to placeholders and embed these into the specification as references. Soon, you'll be able to create your own templates that others can import through a similar mechanism.

<div class="text-center">
    <figure class="figure">
        <img width="1000" src="/assets/images/deneb/preview/editor-simple.png" class="figure-img img-fluid rounded" title="Deneb's editor, showing a Vega-Lite specifiation that generates a simple bar chart">
    </figure>
    <figcaption class="figure-caption">The documentation site also <a href="https://deneb-viz.github.io/simple-worked-example" target="_blank">has a worked example</a> if you want have a look at how to get started.</figcaption>
</div>
<br/>

## We Have Charticulator Now - Why Would I Need Deneb?

Good question! Charticulator is a well-implemented grammar of graphics UI, which is an incredibly rare thing to come across. I'm a huge fan of it, and I hope you find it as useful as I do... I'm certainly going to keep using it in my work 😀

With it being interface-driven, we might sometimes be limited by what the interface exposes to us. It's more extensible than the core visuals but we still might hit a wall when we want to do something very specific.

For anyone who has hit the aforementioned wall when, say, transforming data in Power Query, you can jump down into the <a href="https://docs.microsoft.com/en-us/power-bi/transform-model/desktop-query-overview?WT.mc_id=DP-MVP-5003712#advanced-editor" target="_blank">advanced editor</a> and write directly in <a href="https://docs.microsoft.com/en-us/powerquery-m/?WT.mc_id=DP-MVP-5003712" target="_blank">M</a>. The conventional visuals and Charticulator don't have a capability for this; going to the low-level or 'pro mode' visuals in Power BI requires you to learn the <a href="https://docs.microsoft.com/en-nz/power-bi/developer/visuals/?WT.mc_id=DP-MVP-5003712" target="_blank">visuals SDK</a>, which can be a lot for people to take on for a specific use case.

If I think about how straightforward visual solutions are within Power BI in terms of effort or cognitive load for you as a creator, I see Deneb as an additional pillar in the bridge between conventional visuals and R/Python (with full-on visual development all the way over the other side):

<div class="text-center">
    <figure class="figure">
        <img width="1000" src="/assets/images/deneb/preview/where_stuff_fits.png" class="figure-img img-fluid rounded" title="My world-view of where options fit within Power BI, in terms of complexity. Yours may be different 😉">
    </figure>
</div>

You will be able to dig deeper in terms of what you can do with a visual design, at the cost of having to use an editor rather than an interface. As JSON is a data structure rather than a programming language, it is more intuitive for data folks to work with. And because Vega is also used much more widely than within Power BI, it's also possible to dip into a much wider pool of examples and knowledge online, if you're learning.

Vega can also do things like transformation of your input data to do statistical analysis, much like R or Python. Here's a live example - we haven't had one of those in this post yet:

<div class="text-center">
<iframe width="800" height="486" src="https://app.powerbi.com/view?r=eyJrIjoiNTRhY2ZlMzctZWM4OC00MGU2LThhODktYTc2MDYyNmRhYzc5IiwidCI6IjUzYmJlMGQ3LTU0NzItNGQ0NS04NGY0LWJiNzJiYjFjMjI4OSJ9&pageName=ReportSection71b22ef068305f0fa1d3" frameborder="0" allowFullScreen="true"></iframe>
</div>

In the above report, we start with something simple, like a measure and a sampling column to get the right level of grain, and then produce a distribution plot with some data-driven annotations based on the aggregates of these transforms (page 1). We can re-use the specification for unique values of another field to produce <a href="https://vega.github.io/vega-lite/docs/facet.html" target="_blank">facets</a> or small multiples (page 2). In our example, there's quite a bit of geographic variability that the summary-level visual doesn't uncover, but once we have our design, it's very straightforward to add this context within the same specification. I've also added a line to track mouse position and display a tooltip.

If you're keen to see the inner workings of this one, <a href="https://gist.github.com/dm-p/899c1b47bd994912fcdb863d858cda82" target="_blank">here's a link to a Gist</a> containing the raw specification. I built this visual iteratively, starting with a <a href="https://vega.github.io/vega-lite/docs/density.html#example-density-plot" target="_blank">simple density plot example</a>, and layering-up up the functionality and theming to communicate particular my data story.

## What's Next?

I appreciate that downloading a visual from a website is not ideal for a lot of folks. I want Deneb to be available in AppSource and I'm developing it with a view to certifying it when it does get submitted. This hinges on the assumption that the latest rules will allow it to happen, but I'll do my best to get it over the line.

Firstly, I'd like to make sure that the preview works well for a wider audience before I start the AppSource process, which is why I'm starting with this public preview. If there's anything that we need to iterate quickly on, the submission, review and publication process through AppSource is a little more onerous than this allows.

I'll also be looking to get more functionality chipped-off before then:

-   Ability to generate a JSON template that can be imported by other users
-   Additional APIs for Power BI interactivity, such as data point selection and the context menu
-   ...and anything else that comes up and needs addressing

---

So, there's still a little bit more to do before we get to general availability through AppSource. Although, it has taken me a long time to get to this point, and I'm incredibly grateful for the support of <a href="https://twitter.com/LucidInsightsNZ" target="_blank">Jonathan Kapoor</a>, <a href="https://twitter.com/KerryKolosko" target="_blank">Kerry Kolosko</a>, <a href="https://twitter.com/Mike_R_Carlo" target="_blank">Mike Carlo</a>, <a href="https://twitter.com/HavensBI" target="_blank">Tom Martens</a> & others for their willingness to alpha test Deneb and provide valuable feedback.

And now it's your turn to have a go - I hope that the results of our work can help you with yours. And, if you have anything cool you want to show me, please let me know! The whole point of Deneb is to allow people to unlock their creativity within Power BI, and I'm really looking forward to seeing what comes next 😀

All the best,

DM-P
