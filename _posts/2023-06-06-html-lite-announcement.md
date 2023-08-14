---
layout: post
title: "HTML Content (lite) - now available in AppSource"
author: daniel
categories: [Custom Visual]
tags: [html, htmlcontent]
image: "assets/images/html-content/html-lite-store.png"
description: "Presenting: a less permissive version of HTML Content... but with certification privileges."
featured: false
hidden: false
toc: false
permalink: html-content-lite
---

---

<a href="https://www.html-content.com/" target="_blank">HTML Content</a> was developed and released in July 2020 as a response to the original HTML Viewer visual suddenly disappearing from AppSource. It was always intended to give the creator as much control over the DOM as possible within a custom visual sandbox and as such, this was always going to be something that would not be permitted in a certified visual. With this in mind (and as other folks have developed and released their own certified versions), I've never intended to certify it.

Despite this assertion, this hasn't stopped people asking me for a certified version and I had managed to not budge on my stance for _almost_ 3 years...

As I recently had to spend > 30 hours flying from NZ to the UK, I had a lot of time on my hands to think about things. One thing that got a lot of thought was how I could make a certified version of _HTML Content_ in a way that could be manageable for me.

Having arrived off my flight(s) with a head full of ideas, I then had a significant amount of jetlag to deal with (there's an 11-hour time difference to adjust to). Funnily enough when you have a few mornings where you're wide awake at 3AM and no-one else in the house is, you might as well try and do something productive. As such, I wrote a new version of the visual, submitted it for certification and this is [now available in AppSource as **HTML Content (lite)**](https://appsource.microsoft.com/en-us/product/power-bi-visuals/coacervolimited1596856650797.htmlcontent_certified).

The original version of _HTML Content_ is still available as normal and continues to have the same privileges.

#### Why 'HTML Content (lite)'? Why not just get HTML Content certified?

Certified visuals are not permitted to access external URLs and (amongst some other restrictions). As such the certified version has slightly less functionality than the original _HTML Content_ ('lite') to ensure that it conforms to certification requirements and is guaranteed by Microsoft to be safe.

A lot of people use the existing _HTML Content_ for use cases where external content does want to be loaded - such as loading images, embedding videos and so on - if I were to proceed with certifying this existing version, then a whole bunch of existing reports would break. As such, anyone who wishes to move to the new version for the benefits can do so on their own schedule.

#### What can't I do with this version vs. the original version?

The key difference between versions is that _HTML Content (lite)_ sanitises the HTML to remove or prevent content that is not approved for certified visuals. You can see which HTML tags are permitted (and restrictions on any attributes) on the Visual Editions page on the HTML Content website, or by downloading the sample workbook from <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/coacervolimited1596856650797.htmlcontent_certified" target="_blank">the AppSource listing</a>.

#### What about support?

Because I develop and maintain my visuals in my free time and for free of charge, _HTML Content (lite)_ is no different. This is released under the <a href="https://en.wikipedia.org/wiki/MIT_License" target="_blank">MIT License</a>, which means that it's provided without any formal support or warranties (although I will endeavour to fix any bugs that are discovered in as timely a manner as I can manage).

If you want an HTML visual with more formal support, then you should consider one of the following visuals by these other vendors:

- <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/wa200002144" target="_blank">Shielded HTML Viewer by Nova Silva</a>
- <a href="https://appsource.microsoft.com/en-us/product/PowerBIVisuals/WA200002071" target="_blank">HTML VizCreator Cert by BI Samurai</a>
- <a href="https://appsource.microsoft.com/en-us/product/power-bi-visuals/kteamsolutionsgmbh1585769979285.htmlcssjsviewer" target="_blank">HTML & CSS Viewer by K Team Solutions GmbH</a>

---

If you've been hanging out for it, I hope that you enjoy using _HTML Content (lite)_ and the certification benefits it bestows.

All the best,

DM-P
