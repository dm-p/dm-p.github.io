---
layout: post
title:  "1,000m View of the Custom Visuals SDK"
author: daniel
categories: [ Custom Visual SDK, Tutorial ]
tags: [ node, pbiservice, toolchain, certificate ]
image: 'assets/images/unsplash/jack-sloop-eYwn81sPkJ8-unsplash.jpg'
description: "A short overview of how the custom visuals SDK looks for the developer."
hidden: false
permalink: sdk-high-level-view
---

<a style="background-color:black;color:white;text-decoration:none;padding:4px 6px;font-family:-apple-system, BlinkMacSystemFont, &quot;San Francisco&quot;, &quot;Helvetica Neue&quot;, Helvetica, Ubuntu, Roboto, Noto, &quot;Segoe UI&quot;, Arial, sans-serif;font-size:12px;font-weight:bold;line-height:1.2;display:inline-block;border-radius:3px" href="https://unsplash.com/@jacksloop?utm_medium=referral&amp;utm_campaign=photographer-credit&amp;utm_content=creditBadge" target="_blank" rel="noopener noreferrer" title="Download free do whatever you want high-resolution photos from Jack Sloop" class="text-right"><span style="display:inline-block;padding:2px 3px"><svg xmlns="http://www.w3.org/2000/svg" style="height:12px;width:auto;position:relative;vertical-align:middle;top:-2px;fill:white" viewBox="0 0 32 32"><title>unsplash-logo</title><path d="M10 9V0h12v9H10zm12 5h10v18H0V14h10v9h12v-9z"></path></svg></span><span style="display:inline-block;padding:2px 3px">Jack Sloop</span></a>

If you're looking to develop a Power BI custom visual, then one of the key things to know about is how the solution fits together. 

If you've written code before then you'll know that most of the time, you can test and debug this locally on your machine, and there are specific tools that your editor might have for this. With the custom visuals SDK, it's a little different and I've developed the following diagram over time to make the conversation a bit easier:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/power-bi-dev-architecture-high-level.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">How the Power BI custom visual SDK fits in with the Power BI Service</figcaption>
    </figure>
</div>

## Local Development Environment

As mentioned above, your local machine is where the visual code is developed, and executed from.

* You install [Node.js](https://nodejs.org) to host the Power BI custom visuals SDK.
* The [Power BI custom visuals SDK](https://github.com/microsoft/PowerBI-visuals-tools) provides you with tooling to develop your visual on Node.js, including installation of dependent [Node.js packages](https://www.npmjs.com/). Note that the SDK is a Node.js package in itself.
* You use the `pbiviz` command provided by the custom visuals SDK to manage operations such as creating new projects, creating your certificate and starting the visual host on your machine.
* The certificate is used to securely connect the visual host on your machine to the Power BI Service.

## Power BI Service

The Power BI Service is where you debug your visual code. Well... it does a lot more than that... but when you're developing custom visuals, that's the part it plays. It provides a special Developer Visual, which is available in the *Visualizations* pane:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/developer-visual.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Location of the Developer Visual</figcaption>
    </figure>
</div>

There are some conditions that need to be met to allow this to be available, and functional:

* You need to enable the visual on a [per user basis through your settings](https://docs.microsoft.com/en-us/power-bi/developer/custom-visual-develop-tutorial#testing-the-custom-visual) in the Power BI Service.
* You need to have uploaded a report and dataset to the Power BI Service, and have this open for editing.
* Your local visual host needs to be running (`pbiviz start`) and the code compiles correctly.
* Your local certificate needs to be valid for the SDK you're running.

If any of the above criteria aren't met, you either be able to select the visual, or you'll get an error explaining that the visual server can't be contacted. See *[Gotchas with the Local Certificate](#gotchas-with-the-local-certificate)* below for more details on the latter.

## Avoiding the Dragons

Providing that you have everything as above, the SDK can securely talk from your machine to your report in the Power BI Service. Each time, you start the visual host, or save changes to your code, the SDK is doing the following:

* Compling your code
* Packaging it
* Sending it up to the developer visual through the secure connection

Once this is done, reloading your visual through the controls (or automatically if you've toggled auto-reload) picks up the new packaged code and re-runs the visual constructor.

## Gotchas with the Local Certificate

Seeing this when adding a developer visual to your report?

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/developer-visual-error.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">I think something might be wrong...</figcaption>
    </figure>
</div>

It's pretty much what it says. The developer visual can't see the local visual host running on your machine. I usually check the following first:

* The visual host is running.
* The visual host tells me that my code has compiled correctly.

After that it can become a little more involved. The most common cause after that is an issue with the certificate, and I then consider the following:

* Whether I've updated the version of the visuals API I'm using (done through `pbiviz update x.y.x`, where `x.y.z` represents API version).
* Whether I've recently reinstalled or updated the SDK (`powerbi-visuals-tools`).

If either of these has happened, then your local certificate is no longer valid and needs to be regenerated. Once you've done this and run `pbiviz start` you should be able to refresh your developer visual and be happy again (hopefully your code's compiling successfully!).

At this point, it's likely to be something to do with the network. Beyond the above, I've only run into proxy issues at this point, and that's a word you need to have with your network folks (I certainly can't claim any expertise in this space).

## But... Why Can't I Do This All Locally?

This isn't something that comes up a lot as a question, as it all kind of makes sense in the context of the Power BI ecosystem. Or at least it does to me. The above does mean that you at least need a free Power BI license to access the Service and some people may not have access to them.

If you really want to see your results locally, you technically *can*. When you make a change to your code, you can run:

```pbiviz package```

This will generate a `.pbiviz` file that you can load into your Power BI Desktop report. 

*Should* you, though? There's no developer tooling in Power BI Desktop to allow you to debug, so it's not a viable course of actionunless you like to debug slowly and painfully.

Further rationale for managing this in the Power BI Service (all speculation on my part):

* Custom visual code is executed client-side in the user's browser.
* While Power BI Desktop is not a browser, strictly speaking - it uses the same techniques to render all visuals.
* You can also develop custom visuals using R, but Power BI essentially surfaces any output as HTML, even if the R code runs server-side.
* For client-side JavaScript, web browsers offer comprehensive debugging tools.
* As the Power BI Service is hosted in a web browser and hosts visuals, it's a sensible location to carry this out.
* The alternative would be hosting an instance of the Power BI Service locally as a dependency, and that's going to get complicated for all kinds of reasons.
* The developer visual and custom visuals SDK can have their own release cadences that don't align with either Desktop or Service, and it's easier to deploy these outside of Power BI Desktop if critical changes or fixes need to be deployed. We all get excited when there's a new release of Desktop, so can you imagine your disappointment if it only contained fixes for the custom visuals SDK, and not something really cool like small multiples? I'm sure we'll get those one day...

If you have your own reasons (maybe even official ones!) please let me know and I'll update this list accordingly.

## Wrapping Up

We've outlined at a high-level, how the local development tooling works with the Power BI Service when thinking about developing custom visuals. We've also covered off some of the things that can go wrong when trying to get both sides talking to each other. I'm hoping that you find this useful and it clarifies the platform a bit more for those interested in learning. 

If there's anything you wish to discuss around this, or suggest for a future subject, please get in touch through the comments below and let's see if we can learn more about it together.

As always, thanks very much for reading!

DM-P