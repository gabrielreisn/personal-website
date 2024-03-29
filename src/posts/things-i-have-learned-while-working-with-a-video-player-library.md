---
permalink: posts/{{ title | slug }}/index.html
title: Things i have learned while working with a video-player library
date: 2021-07-11T03:00:00.000+00:00
tags:
- development
description: ''

---
From the previous month, I have been working with video players for enhancing first-time user experience, and that has been simply amazing for discovering new UX approaches, especially on a timeframe where video content got so much attention with platforms like TikTok and now Instagram changing the product focus to a more video-based thing.

For this article sake, I've been using [https://plyr.io/](https://plyr.io/ "https://plyr.io/") for development, which supports self-hosted HTML5 videos, youtube and Vimeo

From a UX perspective, I've seen a few "autoplay with mute" patterns for making the video more attractive for the final user without frustrating the experience/user focus. Twitter has been doing that for timeline videos and now Facebook is doing the same for stories. In my case since it was something educative for the user about the product, after clicking on the video overlay (btw we kept the controls like the big play button, making it look more like a gif) the video would restart and play with sound, pretty simple, but clever and effective.

## Third-party integrations

From an engineering perspective, well, things are a lot easier nowadays, but one thing can still be a pain: interacting with 3rd-party video providers api's (especially the big ones, yes, Vimeo and Youtube). Both use some iframe for injecting the video. We can think about security reasons of course but that makes customizing it a bit hard, youtube takes advantage of that and adds a lot of junk/ own branding, that's good for them since generates organic traffic, but depending on your use case, can be terrible.

![](https://user-images.githubusercontent.com/13686332/124141946-32dd1f80-da60-11eb-8a46-c0112eb580fd.png)

Fortunately, someone posted a solution on [https://github.com/sampotts/plyr/issues/976#issuecomment-862810641](https://github.com/sampotts/plyr/issues/976#issuecomment-862810641 "Github")

Youtube positions the iframe using an absolute value for the cover, making it possible to stretch the video height at a level where all the branding  (and mainly that suggested video terrible panel) dispairs.

![](https://user-images.githubusercontent.com/13686332/124141883-25279a00-da60-11eb-9689-e6b1c7955ace.png)  
The following snippet made the fix for it

    iframe[id^='youtube'] {
      top: -50%;
      height: 200%;
    }

The whole problem began in 2018 when the `?rel` param was removed   
[https://developers.google.com/youtube/player_parameters#release_notes_08_23_2018](https://developers.google.com/youtube/player_parameters#release_notes_08_23_2018 "https://developers.google.com/youtube/player_parameters#release_notes_08_23_2018")

That made the whole thing more restricted for developers, probably led by business decisions ¯\_(ツ)_/¯

## The aspect ratio

Keeping video size proportion can be incredibly hard (specially when using a lib that implement a lof of UI styles for you. Part of the trade-offs)

On modern browsers the support for `aspect-ratio`is quite good according to [MDN docs](https://developer.mozilla.org/en-US/docs/Web/CSS/aspect-ratio) 

One nice tricky prior to that was setting the padding based on a percentage, forcing the whole layout to perserve the width, which got into this nice table

```
aspect ratio  | padding-bottom value
--------------|----------------------
    21:9      |       42.85%
    16:9      |       56.25%
    4:3       |       75%
    3:2       |       66.66%
    8:5       |       62.5%
```

## React integration

As the lib was originaly designed only to support plain-js, community packages were created to support newer UI libraries / frameworks. 
The one recommended on the official lib doc is https://github.com/chintan9/plyr-react, which allows you to have full control of the video API trhough a ref. The curious part with that integration is regarding re-renders. Since you don't want your video to be flickering frames and re-rendering, make sure you wrap all your custom props inside a `useMemo` hook to prevent those