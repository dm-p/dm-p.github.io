---
layout: post
title: "Deneb 1.3 Generally Available"
author: daniel
categories: [Custom Visual, Development]
tags: [deneb, vega, vega-lite]
description: "Deneb 1.3 is now generally available in AppSource. Here's a rundown on what's new, and what's happening next."
image: "assets/images/deneb/1.3/deneb_1_3_banner.png"
featured: true
hidden: true
toc: false
permalink: deneb_1_3
---

I'm happy to say that it's time for another Deneb update, and 1.3 should now be live in (or making its way down to) your reports! The <a href="https://deneb-viz.github.io/next" target="_blank">website</a> has been updated to include more detailed documentation on all new changes. I'll also fill you in on what's coming up further below in this post.

## What's New?

For a full run-down of the changes, you can look at the website's <a href="https://deneb-viz.github.io/next/changelog" target="_blank">Change Log</a>, but the big stuff is as follows:

### Debug Area

The Visual Editor interface has been modified to accommodate viewers for Data, Signals and Logs in what was formerly known as the Preview Area Toolbar. Similar to <a href="https://vega.github.io/editor/#/" target="_blank">vega-editor</a>, these expose more information about the underlying Vega view and will provide creators with more information when designing and debugging specifications within Power BI.

<div class="text-center">
    <figure class="figure">
        <img src="https://deneb-viz.github.io/assets/images/debug-area-4a7eb90ba78acff4f822a3ec1370cb37.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">The Preview Area toolbar now includes a debugging pane, which exposes information on Vega datasets, signals and logs.</figcaption>
    </figure>
</div>

These changes also make the Preview Area toolbar resizable and collapsible, much like the Editor Pane. As this functionality is now greatly increased from before, this is being renamed as the **Debug Pane**.

As this change affects a lot of the existing documentation, the relevant areas are updated with sections pertinent to the Debug Pane, but a short overview is as follows:

- The **Data** pane provides visibility of data sets from the Vega view.

- The **Signals** pane exposes signal details from the Vega view, which will also include things like parameter bindings.

- The **Logs** pane exposes anything that the Vega logger records for the specified log level.

  - Valid levels are **None**, **Error**, **Warn** (default) and **Info**.
  - As a result of this work, it was discovered that some scenarios - such as duplicating params by putting them at the top level for layered plots - were not being correctly trapped and Deneb would 'white out'.
  - The resilience around trapping these errors has been improved and they should display an appropriate error state in the visual (and may expose more details around in the Logs pane).

### Individual Report Theme Colors

A new expression, `pbiColor` has been added. This allows you to access your report theme's colors by a (zero-based) index. There is also an optional parameter, which will allow you to darken or lighten the color by this value (allowing similar results):

<div class="text-center">
    <figure class="figure">
        <img src="https://deneb-viz.github.io/assets/images/pbiColor-simple-grid-6f9a21a763d3d190093f064bc8fe9315.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">The <code>pbiColor</code> function allows you to access individual theme colors using a zero-based index.](deeper-concepts/img/pbiColor-simple-grid.png "The pbiColor function allows you to access individual theme colors using a zero-based index.</figcaption>
    </figure>
</div>

Please refer to the <a href="https://deneb-viz.github.io/schemes#expression-based-access-using-pbicolor" target="_blank">Theme Colors & Schemes</a> page for further details.

### Include Preview Image in Exported Template

When exporting a template, you can now opt to include a preview image, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="https://deneb-viz.github.io/assets/images/export-enter-template-information-preview-image-739ab0b9d96e44a419115324bd584a49.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Templates can now provide the option to include a base64-encoded preview image of the current window.</figcaption>
    </figure>
</div>

Images are generated <a href="https://vega.github.io/vega/docs/api/view/#image-export" target="_blank">using the Vega View APIs</a>. The generated image will be how the image currently looks in the editor, and are capped at a maximum of 150 x 150 pixels (using the largest dimension to constrain the aspect ratio within this area). This is documented in more detail on the <a href="https://deneb-viz.github.io/templates#including-a-preview-image" target="_blank">Templates</a> page, but the high-level overview is as follows:

The generated image will be encoded in your template using base64, so there's a couple of things to consider here:

- This can potentially increase the size of your JSON template considerably.
- This image may potentially expose information about your data if included.

As such, the option is disabled by default and you will need to opt-in.

### Other Site Updates

The community and resources around Deneb are growing, which is awesome news for report creators. The <a href="https://deneb-viz.github.io/community/resources" target="_blank">Resouces and Examples</a> page has also been updated with the following entries:

- Enterprise DNA have launched the _<a href="https://portal.enterprisedna.co/p/introduction-deneb" target="_blank">An Introduction to Deneb: Declarative Visualization in Power BI</a>_ course.
- <a href="https://github.com/shadfrigui/vega-lite">Shad Frigui | Vega-Lite</a> - GitHub repository of <a href="https://twitter.com/shadfrigui" target="_blank">Shad Frigui</a>'s Vega & Vega-Lite examples, including some for Deneb.

---

## What's Happening Next?

1.3 will be the last significant update under Deneb v1, and any subsequent releases will focus on fixing important issues, if any come up.

When I orginally started on this idea of integrating Vega & Vega-lite into Power BI a couple of years ago, I was hoping that I could take the already fully-featured <a href="https://vega.github.io/editor/#/" target="_blank">vega-editor</a> and integrate it with a visual dataset and that would be it. Of course, nothing is ever that simple...

The technical constraints around how custom visuals are hosted within Power BI meant that this wasn't possible and the resulting journey to make Deneb viable and getting it into [public preview last April](/deneb_public_preview) was quite a lot of work. In making the incremental steps to [1.0 GA](/deneb_nov_2021) (and up to 1.3), I've tried to take lessons I've learned in improving my development skills and understanding the limits of the Power BI visuals SDK and make some significant changes within the codebase each time.

I try to have a good head-start on each release when one becomes generally available, and I was making steady progress on what I believe would be a good set of features for 1.4, However, these have been complicated to deliver due to the legacy of what I've had to do to get us where we are today. The ability to iterate is not as agile as it could be, and despite making many changes under the hood to try and improve this, it's getting harder to add value to Deneb in its current form with the amount of free time I have to work on it.

As such, I've decided that it's now the right time to focus my effort and planning on what a Deneb v2 looks like. It is early days, as a lot of what I'm currently doing is going back to basics to determine feasibility of some of the things I've had on my list for a while in a more controlled and isolated manner before I attempt them in a larger context of the current codebase.

### What to Expect

A lot of the changes for 2.0 will be underneath the surface and focus on making the codebase more maintainable; not just for me, but for others who may wish to contribute. In doing this, it should also result in much better performance for the end user.

In terms of features that will have some user impact, expect to see improvements along the following lines:

- Much better autocompletion within the editor.
- Light/dark mode.
- Direct downloading of templates (if permitted by tenant admin).
- Further interactivity enhancements, including Drilldown, and (limited) Field Parameter support.

There will be other features that I'll add to the list once I've determined their feasibility. As always, if there's something you want, then please <a href="https://github.com/deneb-viz/deneb/issues" target="_blank">create an issue for it</a> and we can take a look.

### What not to Expect

While I'm planning to put a lot of effort into the next version, the offering of Deneb is not really going to change very much from what it currently does; the aim is to make the development experience better and continue to provide a good balance of functional breadth without having to jump into writing TypeScript and lots of boilerplate code for common use cases. I've certainly saved a lot of time producing visuals for clients and the community through Deneb rather than going to the SDK.

I'm not planning to make Deneb more UI-driven, for example. We already have <a href="https://charticulator.com/" target="_blank">Charticulator</a> and now other visuals like <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/akvelon.plotlyjsvisualbyakvelon" target="_blank">PlotlyJS</a> and <a href="https://inforiver.com/charts/" target="_blank">Inforiver Charts</a> for no-code chart building. I like that the visual layer is getting much more in the way of choice in this area from organisations that can invest actual money and resources into delivering these experiences, and I'm hopeful that we'll continue to see these solutions become even more powerful over time.

### When to Expect

Right now, that's a tough question to answer, and depends on the complexity of what we're trying to do vs. the reality of being able to achieve it. I'll be taking the next 2 months to explore this and should have a better idea of ETA after these experiments have been completed. I'll be sure to update with more detail when I know.

Once I have my ducks in a row, I'll also be making a version 2.0 project board publicly available, and will write a follow-up on how you can see the 2.0 scope, and progress against it. It's anticipated that 2.0 will have a longer alpha/beta channel period than the current 1.x releases, for those who wish to help test and confirm that the new version is progressing as we want.

---

As always, thanks to you all for reading, and for using Deneb. I hope that you continue to find these updates useful for your work, and they help you to keep creating great content with the awesome Vega languages. Here's looking forward to 2.0!

All the best,

DM-P
