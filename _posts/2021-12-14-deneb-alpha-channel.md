---
layout: post
title: "Deneb Alpha Channel Build Availability"
author: daniel
categories: [Custom Visual, Development]
tags: [deneb, vega, vega-lite]
description: "The Alpha Channel Build is a means of being able to test and feed back on changes as soon as they are made rather than waiting for AppSource releases."
featured: false
hidden: false
toc: false
permalink: deneb_alpha
---

When Deneb was in public preview, I made (roughly) monthly releases available for download and manual install. Through this process were able to iterate quickly, so that folks who were looking to start adopting or picking up changes could do so.

Once a visual gets published in AppSource, its unique ID is "reserved", so if you try to manually load any version of the visual into a report, Power BI will always check AppSource to ensure that you're getting the "latest" version, and this is how custom visuals stay up to date without any intervention from you.

For visual developers, <a href="https://docs.microsoft.com/en-nz/power-bi/developer/visuals/submission-testing?WT.mc_id=DP-MVP-5003712#testing-a-new-version-of-a-published-visual" target="_blank">there are ways for us to verify before submission</a>, but it's not a fun process for anyone wanting to try out a different version of an AppSource visual in earnest. The submission process for updates can also take a while to get from me to your reports, and if someone finds a bug or has feedback on a new feature after the fact, then this can compound the time taken for you to see these changes at the other end, as we need to re-submit for any remediations to get through.

## What I'm Trying to Do

Although I do have a QA background, as the main developer I'm not best suited to overseeing all quality assurance and I may not always have the right answer for how something should be implemented. Having the fresh eyes of other users is also incredibly valuable.

As such I've been thinking about how I can keep a feedback loop for any users who are keen to stay on the bleeding edge of things and happy to provide me with valuable feedback before updates reach a wider audience in AppSource.

I've usually done this via private messaging with a few people throughout development so far, but this isn't necessarily visible for others, and it probably makes sense to try and consolidate things in the Deneb repository's issue tracker so other people can weigh-in on things that might come up.

While there are very few visual developers within the Power BI community, there are a lot of visual consumers, and they may wish to help provide some input on Deneb as we continue to evolve it. I understand that not everyone wants to have to learn how to develop custom visuals or learn how GitHub works for source control, but might be okay with trying things out and creating issues for anything that might need tweaking or improving and helping me understand more about how Deneb is being used.

## How We're Going to (Try to) Do It

As part of working on version 1.1, I've introduced a continuous integration (CI) process for any changes that get pulled into the main branch of code, and will ultimately end up in the next published version of the visual.

This will automatically package the code into a visual (.pbiviz) named **Deneb (Alpha Channel Build)**, which will have its own unique ID and icon - meaning that it can be side-loaded into your reports so it doesn't replace any existing instances of the AppSource visual, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/deneb-alpha-channel-icon.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Adding the 'Deneb (Alpha Channel Build)' visual to a report shows a different visual instance in the palette</figcaption>
    </figure>
</div>

Adding the visual to the report canvas shows some subtle differences in the landing page also:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/deneb-alpha-channel-landing.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Example of 'Deneb (Alpha Channel Build)' landing page, after adding the visual to the report canvas</figcaption>
    </figure>
</div>

## Important Things to Consider (if You're Interested)

If the following details below give you pause, then it is recommended that you use the regular AppSource version. For those who are interested, here's what you need to bear in mind with respect to usage and installation of the Alpha Channel Build version:

- **The most important thing to note is that this visual is not intended for production use**: it is intended for you to be able to try what's coming, but also to feed back on anything that might not look correct or work well so that we can head-off these kinds of issues before we embark on a lengthy submission cycle.

- I cannot stop you from using it in a production environment, but I won't be able to prioritise supporting any issues that come out of you using it in production - because Deneb is free and open source, it is not my day job and doesn't pay the bills, so I can't drop everything I'm doing to fix something that may not be quite ready yet. Speaking of which...

- I make best effort to ensure that the Alpha Channel Build is reliable, but some features may not be fully implemented and/or may not be stable. I'm still keen to hear about anything you see here, but it may already be something I'm aware of and am activerly working on.

- Because the Alpha Channel Build has a completely different ID to the AppSource version, it doesn't have any of the specific certification benefits of that version (when they happen). As such you will not be able to leverage any features of Power BI that depend on certified visuals.

- As documentation is quite a big task, I don't get to formally document changes until I'm ready to package the release for AppSource. As such, you may need to do a little bit of discovery to understand what's in the build that has changed.

- Builds are not made regularly (e.g. nightly) as code changes are infrequent. This may change but for now, you can get notified of a new Alpha Channel Build via GitHub (details further below).

- The <a href="https://deneb-viz.github.io/privacy-policy" target="_blank">privacy policy</a> and <a href="https://github.com/deneb-viz/deneb/blob/main/LICENSE" target="_blank">license</a> are the same as for the AppSource version.

## How You Can Help

From a testing and verification perspective, I'm mostly keen to know if you spot any bugs or have feedback on how well a particular feature works.

As mentioned above, the best way to find out what's being worked on (or delivered) in the Alpha Channel Build since the last release to AppSource is to look at <a href="https://github.com/deneb-viz/deneb/milestones" target="_blank">the milestones page</a> in the GitHub repository's <a href="https://github.com/deneb-viz/deneb/issues" target="_blank">issues tracker</a>. In the milestone will be the issues currently being worked on, as well as those that have been closed. Closed issues _should_ be completely available for use in the current Alpha Channel Build.

If you need to let me know about anything, please <a href="https://github.com/deneb-viz/deneb/issues" target="_blank">create an issue</a> with your feedback. It is also worth having a quick look through the list of open issues to see if someone else has raised what you've noticed. Where possible, please include the version/build of the visual you're using. You can do this by:

- Right-clicking the visual in the pane and choose **About**. Power BI will open a dialog with the visual details, e.g.:
<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/deneb-alpha-channel-about-dialog.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Example of the details for a Power BI visual</figcaption>
    </figure>
</div>
- Selecting the text in the _Version_ field and copying to the clipboard with [Ctrl + C].
- Pasting the text into the issue you're creating.

I'll be working on some issue templates soon, which should help streamline this process, but for now the best thing you can do is to help me help you by including as much information as you can.

New issues will be triaged and scheduled. Depending on their priority and/or severity, they might not get fixed immediately and could be scheduled for later on. Please do not be discouraged by this if it happens - it's important for me to know how people are using Deneb and provide them with better ways of using it but I only have a finite amount of time to work on it.

For anyone who wishes to help from a coding perspective, I'll be working on some documentation after 1.1 to try and standarise how we could potentially work collaboratively, as well as how to set up a development environment. If you are interested before then, please feel free to reach out and let me know. I'll try to do what I can to help you get started.

## How You Can Get (and Use) It

The latest alpha channel build will always be available from <a href="https://github.com/deneb-viz/deneb/tree/alpha" target="_blank">**Releases** in Deneb's GitHub repository, with the `alpha` tag</a>. Alternatively, you can use the following URL:

<a href="https://bit.ly/deneb-alpha" target="_blank">https://bit.ly/deneb-alpha</a>

This will present you with the page the latest Alpha Channel Build. This will contain a summary of changes made since the last build, which can be followed.

The .pbiviz file is downloadable from the list of assets at the bottom, e.g.:

<div class="text-center">
    <figure class="figure">
        <img src="/assets/images/deneb/deneb-alpha-channel-release-page.png" class="figure-img img-fluid rounded">
        <figcaption class="figure-caption">Example of 'Deneb (Alpha Channel Build)' landing page, after adding the visual to the report canvas</figcaption>
    </figure>
</div>

This can then be installed into your report by <a href="https://docs.microsoft.com/en-us/power-bi/developer/visuals/power-bi-custom-visuals?WT.mc_id=DP-MVP-5003712#custom-visual-files" target="_blank">importing from the downloaded file</a>.

Once installed, this will overwrite any versions of the Alpha Channel Build that you will have loaded in the report. Note that visuals do not fully re-initialise until they are "created" on a report page, so you can either navigate away from the current page and back if you need to refresh an instance of an Alpha Channel Build visual, or add a new one to the current page.

If you don't mind the emails, you can opt to subscribe to release noifications by watching the Deneb repository - <a href = "https://docs.github.com/en/account-and-profile/managing-subscriptions-and-notifications-on-github/setting-up-notifications/about-notifications" target="_blank">here's the appropriate GitHub help link for setting this up</a>.

---

If you've made it this far, thanks so much for reading and your continued interest in Deneb. I hope you're enjoying it!

All the best,

DM-P
