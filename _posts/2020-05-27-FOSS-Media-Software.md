---
layout: post
title:  "The Best Free and Open Source Media Software"
date:   2020-05-27 15:51:00
categories: media streaming foss cli linux
---

The FOSS ecosystem's many benefits present only a few minor annoyances for sysadmins - the most significant of which is the need for research, as well as trial-and-error, when investigating new software to employ in your stack.

If you are in need of suggestions for FOSS media software, this article is for you.

## TLDR

```sh
$ pacman -S sox mpv vapoursynth ffmpeg mkvtoolnix-cli graphicsmagick
```

### Audio

#### SoX (LGPL)

[SoX](http://sox.sourceforge.net/) - or, Sound eXchange - is an LGPL application for processing sound. It supports every free (and some non-free) audio codecs, and employs by far the best resampler out there today, `libsoxr`. Below, we see SoX totally annhilating expensive, proprietary "professional" audio software, Ableton Live, in a sampling rate conversion test.

![](/sox-src.jpg)

### Containers

#### Matroska

If you want to stay sane working with media in the FOSS realm, you'll need to be using [Matroska](https://www.matroska.org/). A fully open and highly extensible container format, it is just about as free as free software gets.

At a minimum, make sure you have [`mkvtoolnix`](https://mkvtoolnix.download/) laying around for easy and flexible muxing, demuxing, and metadata tasks.

### Images

For many years, the undisputed FOSS image processing king was `ImageMagick`, however over the years the project has failed to address meaningful inefficiencies and bugs within the software. This spawned the `GraphicsMagick` fork, which I would recommend to most users. Only those who need its deep colors features should consider sticking with the old and tired ImageMagick.

#### GraphicsMagick (MIT)

If you are needing to process images in batch, [GraphicsMagick](http://www.graphicsmagick.org/) is your starting shortstop. Its use of OpenMP means that CPU-bound tasks scale linearly as processor cores are added.

### Playback

#### mpv

![](/mpv.jpg)

The VideoLan folks have always done a fantastic job with the VLC player, however if you are serious about working with media you'll want to move on over to the new king of FOSS media playback, `mpv`. Those 10-bit color anime folks love it, so you know it must be good.

`mpv` has a world-class plugin system that enables users to contribute scripts and plugins in either Lua and NodeJS. This is very useful for media analysis and for media processing automation.

### Video

Video encoding and DSP today is largely centralized within the FFmpeg project, however more advanced operations will require additional tooling.

#### FFmpeg (LGPL/GPL/non-free)

[FFmpeg](https://ffmpeg.org) is one the most significant and prolific FOSS projects out there today. Known as the "Swiss Army Knife" of multimedia coding, FFmpeg is an indispensible tool for your media workflow, and will likely be a dependency for building many other great media tools.

You'd be pretty crazy to attempt any video work within the FOSS ecosystem without a recent build of FFmpeg.

#### VapourSynth (LGPL)

![](/vs.jpg)

Many think that *if you can't do it in FFmpeg, it probably can't be done*, but that's probably because they don't know about [Vapoursynth](http://www.vapoursynth.com/).

VapourSynth is a powerful open source Python framework for advanced video frame processing and other visual DSP operations. It's got some of the best post-production and color tooling out there, and a really healthy community of video freak contributors who dogfood their own work in meaningful ways. It is in many ways a replacement for the aging, less-performant AviSynth framework.

A few notes about VapourSynth... first, this is definitely software developed by *media engineers*, so you will discover bugs. Expect releases to come out very frequently, and definitely don't skip reading the docs. Also, some operations on VapourSynth will take a *hilariously* long amount of time to complete, though the results are often worth the wait.
