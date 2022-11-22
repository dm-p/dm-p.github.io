---
layout: post
title: "Happy 1st birthday, Deneb!"
author: daniel
categories: [Custom Visual]
tags: [deneb, vega, vega-lite]
description: "As Deneb has been GA in AppSource for a year, I thought it'd be a good time to catch-up!"
image: "assets/images/deneb/deneb-1-year.png"
featured: true
hidden: true
toc: false
permalink: deneb_ga_1_year
---

As I sit here, it's incredible to think that a year has gone by already since Deneb became generally available (GA) in AppSource. It took a little while to get there though, and for anyone not familiar with Deneb's history, here's a quick overview:

- <a href="https://vega.github.io/vega/" target="_blank">Vega</a> and <a href="https://vega.github.io/vega-lite" target="_blank">Vega-Lite</a> languages are a high-level "visualisation grammar" for building web-based visuals without a need to deep-dive into learning web development.

- Both languages are from the work at the <a href="https://idl.cs.washington.edu/" target="_blank">UW Interactive Data Lab</a>, and its many alumni and contributors who continue to evolve it: led by <a href= "https://twitter.com/kanitw" target="_blank">Kanit "Ham" Wongsuphasawat</a>, <a href= "https://twitter.com/domoritz" target="_blank">Dominik Moritz</a>, <a href= "https://twitter.com/arvindsatya1" target="_blank">Arvind Satyanarayan</a> and <a href= "https://twitter.com/jeffrey_heer" target="_blank">Jeffrey Heer</a>, but be sure to check the contributors (<a href= "https://github.com/vega/vega/graphs/contributors" target="_blank">V</a> \| <a href= "https://github.com/vega/vega-lite/graphs/contributors" target="_blank">V-L</a>) to see just how many folks have helped get the languages to where they are today. And, without such great and versatile languages, we wouldn't have Deneb!

- It's been about two and a half years since <a href= "https://twitter.com/the_d_mp/status/1235761784650231808?s=20&t=r8vMGM90uGq-U1xONad8Xg" target="_blank">I had the notion</a> of making a visual that could parse Vega specifications in Power BI...

- About a year and a half ago I'd spent enough time on it to [make it available for public preview](deneb_public_preview).

- And, after some further iteration, it became available in AppSource (and certified) [in November last year](deneb_nov_2021).

From inception through to GA and beyond, I've been keeping track of some of the things that have happened and thought it might be good to recap what's taken place (that I know of).

## Community: from preview to GA

Power BI's community is awesome, and one I'm really fortunate to be a part of. With Deneb, the Power BI community did their thing: got curious about it, explored the Vega languages and then started to help others learn how.

> It's probably also worth mentioning here that because Vega and Vega-Lite are not proprietary to Power BI, there's also many places outside of the Power BI community where you can learn from others. You can refer to <a href="https://deneb-viz.github.io/support" target="_blank">Deneb's support page</a> for details of some of the popular communities where you can network and ask for help.

I'm really stoked that Deneb picked up some really strong supporters at the idea stage and helped convince me that it could potentially work enough to finish-off prototyping it and putting it out there. We also gathered some more enthusiasts during the public preview phase and these folks both helped to promote the possibilities through creating and sharing their own content, and became a great source of feedback for moving Deneb towards GA.

Special thanks here go to <a href="https://www.linkedin.com/in/kerrykolosko/" target="_blank">Kerry Kolosko</a>, <a href="https://www.linkedin.com/in/jrescalante/" target="_blank">José Escalante</a> and <a href="https://www.linkedin.com/in/gregphilps" target="_blank">Greg Philps</a> for their early support and great content, and doing the hard yards to help make the languages easier for others to approach in Power BI so early on. Thanks also to <a href="https://www.linkedin.com/in/reidhavens" target="_blank">Reid Havens</a> and <a href="https://www.linkedin.com/in/michaelcarlo" target="_blank">Mike Carlo</a> for inviting me onto their YT channels and presenting to their audiences. Mike has also <a href="https://github.com/PowerBI-tips/Deneb-Templates" target="_blank">started and curated a GitHub repository of Deneb templates</a> that others can contribute to, and this continues to grow. We also got some support during the early stages from Power BI user groups, including:

- <a href="https://www.meetup.com/microsoft-power-bi-uk/events/279121759/" target="_blank">Microsoft Power BI UK</a> - thanks to Leon
- <a href="https://www.meetup.com/power-bi-user-group-wellington-nz/events/279269732/" target="_blank">Excel and Power BI User Group, Wellington NZ</a> - thanks to Phil & Jeff (Jeff particularly for putting a roof over my head for the night)
- <a href="https://www.meetup.com/queensland-power-bi-user-group/events/280019482/" target="_blank">Queensland Power BI User Group</a> - thanks to Craig and Gilbert
- <a href="https://www.meetup.com/austin-power-bi-user-group/events/280213835/" target="_blank">Austin Power BI User Group</a> - thanks Matt, Mike, and Sean
- <a href="https://www.meetup.com/canberra-microsoft-analytics-powerbi-group/events/280610295/" target="_blank">Canberra Microsoft Analytics & Power BI User Group</a> - thanks to Jim, Angela, and Kieran

## Community: from GA to now

Since going to GA, we've seen more people in the community pick-up Deneb and create additional content. There's been enough traction where I'm now struggling to keep up with what people are doing (which is kind of awesome in and of itself), so here's some of the ones that stick out:

- More user groups:

  - <a href="https://www.meetup.com/Portland-Power-BI-User-Group/events/282293767/" target="_blank">Portland Power BI Virtual Meetup</a> - thanks to Greg and Tina
  - <a href="https://www.meetup.com/Seattle-Modern-Excel-Users-Group-MEUG/events/282811339/" target="_blank">Seattle Modern Excel + Power BI User Group</a> - thanks to Stefan
  - <a href="https://community.powerbi.com/t5/Persian-Power-BI-User-Group/Deneb-Develop-Custom-Visuals-Inside-Power-BI/ev-p/2449385" target="_blank">Persian Power BI User Group</a> - thanks to Vahid
    <br/>
    <br/>

- <a href="https://twitter.com/ThysvdW" target="_blank">Thys van der Westhuizen</a> has been creating a heap of content, including <a href="https://twitter.com/search?q=%23bbod%20%23powerbi%20%23deneb" target="_blank">reproducing the Big Book of Dashboards chapter-by-chapter</a>. Thys's site <a href="https://thysvdw.github.io/" target="_blank">also has loads of posts</a> showcasing and explaining further ideas and content.

- <a href="https://twitter.com/shadfrigui" target="_blank">Shad Frigui</a> similarly creates beautiful and functional visuals, and again <a href="https://github.com/shadfrigui/vega-lite" target="_blank">has a site</a> you can visit to have a look in more detail.

- <a href="https://twitter.com/PowerBIguy" target="_blank">Ben Ferry</a> (Power BI Guy) <a href="https://www.youtube.com/playlist?list=PL6oIJxyQvMGTxh4tREeKflcKVlOfGdyim" target="_blank">has a series of videos</a> exploring Deneb, and how to think about enhancing or elevating standard visuals. Ben has also presented on Deneb at Power BI user groups and events.

- At the recent New Stars of Data, Blanca de Erausquin's <a href="link" target="_blank">speaking debut was on Deneb</a> (and it was a fantastic session).

- Enterprise DNA has got behind Deneb in a big way:

  - <a href="https://forum.enterprisedna.co/c/deneb-showcase/58" target="_blank">There's a forum dedicated to Deneb</a> that contains all of the templates that Greg Philps has been demonstrating.
  - They've hosted Deneb-based sessions at their community events, including those from Kerry, Greg and myself.
  - Greg <a href="https://www.enterprisedna.co/courses/introduction-to-deneb/" target="_blank">created an introductory course</a>, which is a great way to see the potential of the languages and how they can be used in Power BI.
  - There's also now a <a href="https://forum.enterprisedna.co/t/deneb-cheat-sheet-for-power-bi/29550" target="_blank">downloadable cheat sheet</a> for common scenarios (also by Greg).
    <br/>
    <br/>

- The team at Workout Wednesday (<a href="https://twitter.com/jsbaucke" target="_blank">Spencer Bauke</a>, <a href="https://twitter.com/MMarie" target="_blank">Meagan Longoria</a>, <a href="https://twitter.com/shan_gsd" target="_blank">Shannon Lindsay</a>, <a href="https://twitter.com/KerryKolosko" target="_blank">Kerry Kolosko</a> and <a href="https://twitter.com/NerdyWithData" target="_blank">Rob Saunders</a>) have prepared and posted several Deneb-related challenges:

  - <a href="https://www.workout-wednesday.com/pbi-2021-w31" target="_blank">2021 Week 31</a>
  - <a href="https://www.workout-wednesday.com/pbi-2022-w13" target="_blank">2022 Week 13</a>
  - <a href="https://www.workout-wednesday.com/pbi-2022-w37" target="_blank">2022 Week 37</a>
  - <a href="https://www.workout-wednesday.com/pbi-2022-w42" target="_blank">2022 Week 42</a>
  - <a href="https://www.workout-wednesday.com/2022-week-45-power-bi-ribbon-chart-advanced/" target="_blank">2022 Week 45</a>
  - <a href="https://www.workout-wednesday.com/pbi-2022-w46" target="_blank">2022 Week 46</a>
    <br/>
    <br/>

- As I'm putting the finishing touches to this post, I've seen that <a href="https://twitter.com/avatorl" target="_blank">Andrzej Leszkiewicz</a> is doing some work with Vega in Power BI and is producing some amazing examples <a href="https://powerofbi.org/deneb-vega-data-visualization-examples/" target="_blank">[website]</a>.

To everyone (especially those I may have missed), thank-you for sharing your learning and experiences, promoting, and presenting about Deneb and the Vega languages, and for helping others to learn and grow. You're all truly awesome :)

> And, if anyone wants to let me know of anyone who I have missed, or if you want to get their (or your) content added to <a href="https://deneb-viz.github.io/community/resources" target="_blank">Deneb's Resources page</a>, please feel free to let me know. I'd love to host it there if you'd like to share it there.

## My experiences of the journey so far (development-wise)

I've talked briefly in various places about some of the rationale and technical challenges around Deneb, but I don't think I've spoken cohesively about it and as such I thought I'd dedicate some time to this, as it may be interesting to some.

#### Rationale

For anyone who has tried to do it, building Power BI visuals using the SDK requires web development knowledge, and while anyone can do this (myself included), it's a very steep curve if you want to explore a visual idea or have to learn on demand. It's also a big step taking a visual idea that works well with a static datset and viewport size, and making it much more dynamic, in the Power BI sense of things.

Ever since I found out about the Vega languages, I thought they'd be a great fit for those of us who like to build our visuals in a more declarative manner, and the idea of a runtime environment in Power BI that houses the languages (avoiding the need to build and test them separately) looked like an ideal middle-ground from my perspective. As Vega is quite extensible, there was potential to integrate some of the interactivity features that are expected from Power BI visuals without running into some of the immediate limitations when using R and Python in Power BI.

#### Reality

Making (what eventually turned into) Deneb turned out to be a lot more complicated than I thought it might be. It's certainly been a labour of love rather than an entrepreneurial endeavour, and completely researched, developed, and supported during my free time (and for free). As such, there will always be concessions that need to be made to get something out there. If I would have waited until I was happy with it, I'd probably still be writing that first version. And I'm glad I did put it out there, as I've already learned so much from those who have been using it.

When I started, I thought that the ideal path would be to leverage work that the existing Vega community had done: <a href="https://vega.github.io/" target="_blank">Vega Editor</a> is a great tool and it would have been better if I could have just lifted and shifted this code into Power BI. It would have made sense - if possible - to be able to re-use (and ideally contribute to) these tools rather than re-inventing the wheel.

I definitely tried to do this first; however, the realities of making this work within a custom visual became apparent quite quickly. Wanting to ensure that the intended solution could work as a certified visual further compounded this (therefore creating more limitations before I began).

Vega-Editor has pretty much everything I think you need for authoring specifications, particularly as it uses <a href="https://microsoft.github.io/monaco-editor/" target="_blank">Monaco Editor</a>, which is Microsoft's editor component behind <a href="https://code.visualstudio.com/" target="_blank">Visual Studio Code</a>. This has all kinds of neat features - like autocompletion from Vega's JSON schemas - that are great for productivity.

After a _lot_ of trying to make it work, I deemed it not possible - at least within the constraints of my knowledge at the time - so had to abandon that idea. I've tried many times since, including getting help internally from MS on how I could approach it, but it just isn't possible to make it work easily within a custom visual without having to load all its dependencies from a remote endpoint (and that's a big no-no for any certified visual).

Because of this, I decided that I would need to focus on creating my own personal interpretation of Vega-Editor, working to these limits.

#### Retrospective

Looking back at how much I dedicated to researching and developing Deneb over the last 2 years or so, I can safely say it's been well over 2,000 hours of my personal time to make it happen. My wife has been incredibly understanding and supportive, and I'm extremely grateful to her to have been able to have the opportunity to dedicate to figuring out how the idea could be developed. I mean... I'm still figuring that bit out, and I'm really happy to see that its genuinely helping people do more inside Power BI. I've had some great feedback from people all over the world, and I hope that many more discover the Vega languages and can see how they can be applied to Power BI as well.

Deneb has been incredibly useful in my work and for all challenges I've had (so far), the Vega languages have been up to the task. I've been fortunate to have been able to use Deneb visuals in a number of projects at <a href="https://www.discoverei.com/" target="_blank">DiscoverEI</a>, as well as helping other folks (from a professional services perspective) to build their own for a fraction of the cost and effort of starting from scratch.

Through learning and using I have my own list of things I'd like to see, and in addition to this, folks have added their own ideas to <a href="https://github.com/deneb-viz/deneb" target="_blank">Deneb's GitHub repo</a>. I'm now trying to learn how to wear a product manager's hat in addition to the development and documentation work; there have been a few big updates since GA, and we're continuing to move forward.

With each of these new releases, I've also been putting a lot of time into refactoring code and endeavouring to find a better editor component that can work in a custom visual, but it just hadn't been possible to get there yet. I was also finding a lot of the newer features I was looking to do in version 1.5 and beyond were made more difficult by the technical debt that had accrued over Deneb's (short) lifetime.

I talked a little bit about these challenges [in the 1.3 release post](deneb_1_3#whats-happening-next), and how I was planning to move forward. This stance hasn't really changed, but despite saying that 1.3 would be the last significant update, there was enough work to warrant a 1.4 release. I just can't stop tweaking this thing...

## What's next?

I was hoping to dedicate some space to start talking about where version 2 is currently at, and what it might look like, but this post has run a little long. I promise I will share some details in a follow-up post, but this might be in a couple of weeks, as I'm currently preparing for <a href="https://difinity.co.nz/" target="_blank">Difinity</a>, which will keep me busy for the rest of this week and most of next week. Bet you can't guess what I'm presenting on...

So, for now, I want to say thanks again so, so much to everyone who has found Deneb useful for their needs. I hope that you continue to create great things and will look forward to what's planned.

As always, thanks for reading. I wish you all the best, and stay safe,

DM-P
