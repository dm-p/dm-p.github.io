---
layout: post
title: "What's Going on with Deneb?"
author: daniel
categories: [Custom Visual, Release]
tags: [deneb, vega, vega-lite]
image: "assets/images/deneb/deneb_banner.png"
description: "It's been a while since we've had an update, so... here's an update."
featured: true
hidden: true
toc: false
permalink: deneb_nov_2021
---

18 months(-ish) ago, I started out with an idea of an idea...

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">I&#39;ve recently had to brush up on my <a href="https://twitter.com/hashtag/reactjs?src=hash&amp;ref_src=twsrc%5Etfw">#reactjs</a> skills for a project and have been curious about the possibility of creating a <a href="https://twitter.com/hashtag/powerbi?src=hash&amp;ref_src=twsrc%5Etfw">#powerbi</a> custom visual with the ability to live-edit a <a href="https://twitter.com/vega_vis?ref_src=twsrc%5Etfw">@vega_vis</a> spec. This is the result of a very quick spike, but... I might see where I can go with it <a href="https://t.co/RtwkPq6VxC">pic.twitter.com/RtwkPq6VxC</a></p>&mdash; Daniel Marsh-Patrick (@the_d_mp) <a href="https://twitter.com/the_d_mp/status/1235761784650231808?ref_src=twsrc%5Etfw">March 6, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

For those who don't know, the <a href="https://vega.github.io/vega/" target="_blank">Vega</a> and <a href="https://vega.github.io/vega-lite" target="_blank">Vega-Lite</a> languages are a high-level "visualisation grammar" for building web-based visuals without a need to deep-dive into learning web development. Both languages are from the work at the <a href="https://idl.cs.washington.edu/" target="_blank">UW Interactive Data Lab</a>, and it's many alumni and contributors who continue to evolve it.

After several prototyping attempts (and dead-ends!), I finally felt that things were okay enough to make the **Deneb** custom visual [available for public preview](/deneb_public_preview) in April this year.

---

Following that announcement I've been quiet on this website, but have been <a href="https://deneb-viz.github.io/changelog" target="_blank">releasing updates</a> roughly every month, including the following additional features:

- Modern tooltips
- Cross-filtering
- Context menu
- Template generation and import
- Zoom-level for "detail-work" in visuals with a smaller footprint
- Pattern fills
- Power BI format string support

This just about brings us to the present day and gets us all up to speed. A lot of what I initially envisaged has been realised, and I'm going to continue adding more features and functionality over time.

If you're looking to learn, it's still early days but creators such as <a href="https://kerrykolosko.com/" target="_blank">Kerry Kolosko</a> and <a href="https://www.youtube.com/watch?v=gqzlEAbXrVU&list=PLf18HMTT3dFhM6EI8Oim-cSYbhHaAvgZs" target="_blank">José R. Escalante</a> are producing amazing content and helping the community to start learning the languages inside Power BI.

Diving deeper, you can start with <a href="https://vega.github.io/vega/" target="_blank">Vega</a> and <a href="https://vega.github.io/vega-lite" target="_blank">Vega-Lite</a> documentation, as well as the Deneb website - <a href="https://deneb-viz.github.io/" target="_blank">deneb-viz.github.io</a> to learn about the Power BI-specific parts.

---

<br/>
<br/>

Oh... I almost forgot!

<br/>
<br/>
<br/>

**I'm so, so happy to announce that Deneb 1.0.0 is <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/coacervolimited1596856650797.deneb" target="_blank">now available in AppSource!</a> 🎉🎉🎉🎉** You'll now be able to find it in Power BI Desktop when you add a visual.

<br/>
<br/>
<br/>

**And it's getting certified! <img src="/assets/images/badge_pbi.svg" title="Power BI Certified" alt="Power BI Certified" />** All the tests have passed and this status should take effect within the next 2 weeks (so a little bit of a longer wait for anyone who needs the badge before they can load the visual into their reports).

<br/>
<br/>
<br/>

So... if you've been holding off getting started, it's now a great time to start experimenting. I'm really looking forward to seeing what else we can do with the visual canvas in Power BI now 🙂

All the best,

DM-P
