---
layout: post
title:  "Audio Mastering Workflow for Linux"
date:   2019-05-05 16:15:00
categories: linux mastering audio workflow
---

This series of posts will detail the audio mastering workflow I employ on my main Linux workstation:

- Watercooled Full ATX Tower

- Intel i9 @ 4.2Ghz

- 64GB ECC RAM

- Arch Linux with the `linux-rt` kernel patches

- [RME Hammerfall DSP 9632](https://www.rme-audio.de/en/products/hdsp_9632.php) PCI Audio Card

- [RME Babyface Pro](https://babyface.rme-audio.de/) USB Audio Interface

## Why?

The answer is simple: *Linux provides the best audio engineering workflow for audio professionals - hands down.* Don't believe me? I'll prove it!

### Audio Engine

Audio is a second-class citizen in every modern OS. From macOS's progressively-worsening "CoreAudio" to Windows' pathetic ASIO implementation, the proprietary software world has fewer reasons every day to coerce you into a non-free software environment.

[JACK](http://jackaudio.org/) allows audio to be your OS's main consideration.

It lets you route *anything* to *any other thing* and does so at minimal to no latency!

### Extensibility Without Compromise

Want to add a new source connection or a track insert? Audio doesn't stop playing and no clicks are emitted just because you wanted to do that.

Want to use Wine to power your Windows VSTs inside your DAW? There's an application for that.

Want to use the `realtime` kernel patches to ensure your latency never extends beyond 1.9ms? Just `yay -S linux-rt` and go brew some coffee. Within an hour you should have a fully functioning realtime kernel OS with incredible latency features. And trust me, you *do* want to use `linux-rt`. Anyone who tells you that audio engineers won't benefit from using realtime patches doesn't in any way understand our plight. Using `linux-rt` I am able to achieve latencies under 2ms at 44.1kHz with 3 periods per block, using my RME Babyface Pro in Class-Compliant Mode.

## How?

It actually is quite simple. However, I do have to call out a caveat:

### CAVEAT

The system I've configured and which has been successful for me, is by no means an "all rounder". By this, I mean that users must go to some lengths to ensure that no extraneous processes are running, no systemd services are operational, etc., unless *it is absolutely necessary* for your audio tasks.

If you use `linux-rt` expecting the same stability for, say, databases and video processing, you'll be very disappointed. To do this work, simply boot back into the stable `linux` kernel that comes with your Arch Linux installation.

### Getting Started

To get started, simply install a new Arch Linux base OS on your audio PC.

Next, compile the `linux-rt` sources by installing the `yay` AUR helper, then runnning:

```
yay -S linux-rt
```

You should also acquire the following realtime-oriented tools:

```
yay -S rtapp rtirq realtime-suggestions cpupower
```

##### Recommended Settings

Enable the realtime services to run at boot:

```
systemctl enable rtapp && systemctl start rtapp
systemctl enable rtirq && systemctl start rtirq
systemctl enable cpupower && systemctl start cpupower
```

Open the `rtapp` config file at `/etc/rtapp/rtapp.conf` and add any binaries for audio programs you use there.

Set swappiness lower than default by running:

```
sudo sysctl vm.swappiness=10
```

Set the CPU governor to `performance`:

```
sudo cpupower frequency-set -g performance
```

Finally, run the `realtime-suggestions` program to see what it thinks about your configuration. It will give you warnings if anything should change.

### Software

#### JACK2 & Cadence

We will be using `jack2` because using `jack` in 2019 is stupid! The multi-threading, MIDI, and other various features of `jack2` are deal-breakers, not nice-to-haves.

Also we will use `cadence` to configure our JACK settings.

![](https://kx.studio/screenshots/cadence1.png)

Get everything you need by running:

```
yay -S jack2 cadence alsa-firmware alsa-utils alsa-tools
```

##### Recommended Settings

For any high-performance real-time audio synthesis work, such as SuperCollider programming, it's strongly encouraged that you run at the lowest sampling frequency possible (I use 44.1kHz and if you use anything higher, you need to [watch this video](https://xiph.org/video/vid2.shtml).)

I am able to place my RME Babyface Pro at 44.1kHz with a 3-period buffer and a block latency of 64 which results in a final round-trip latency of 1.5ms!!! That's right - on consumer hardware, I am running an audio workstation that has a round-trip of under two milli-freaking-seconds! It's a great time to be an audio engineer.

#### non-daw

I love `non-daw`. It's a modular DAW which allows one to roll their own audio workflows.

`non` comes with:

`non-mixer`: A super fast audio mixing engine. It's got an extremely functional [spatial panner](http://non.tuxfamily.org/mixer/doc/spatialization-console.png)!

`non-session-manager`: A session management interface which handles all plugin states, audio interconnections, and routing specifications.

`non-sequencer`: A midi and audio file tracking sequencer which is serviceable for most uses as long as you're not looking to become a Venetian Snares copycat. It's got this novel concept of a "phrase" which is essentially a way to break down compositions into units of musical gestures.

`non-timeline`: A multi-track timeline view which enables lining up all your tracks, components, and outputs, unified by a single clock.

For mastering purposes, I have installed the following:

```
yay -S non-mixer non-session-manager
```

#### Carla: VST Bridge

Carla is the software that allows us to run our Windows VST plugins (which we have **PAID FOR** of course) in Linux without any sacrifices in terms of quality, efficiency, or reliability.

![](https://camo.githubusercontent.com/20961a44e99d7a813837fa1ab5c3ab2395809428/687474703a2f2f6b7873747564696f2e6c696e7578617564696f2e6f72672f73637265656e73686f74732f6361726c612e706e67)

Before installing Carla, let's prepare our system:

First step is to make sure we are reading from Arch's `multilib` repo. To do that, uncomment the multilib repository information in `/etc/pacman.conf`.

Next, we install wine: `yay -S wine-staging`. It is critical that you use `wine-staging` from the AUR and not the production-ready wine binary, because only `wine-staging` supports running VST plugins in RT mode.

Now you can install carla and all the necessary modules:

`yay -S carla-git carla-bridges`

Now, you can use Carla to load Windows VST2 and VST3s (both 32bit and 64bit supported) [with ease](https://kx.studio/Applications:Carla#Usage)!

##### Recommended Settings

For mastering, run Carla's engine is in *continuous rack* mode. This allows a user to order their plugins like on a physical audio rackmount cabinet, and handles all audio routing and latency concerns for you. The tradeoff is that since this is a simplified routing approach, advanced features like infinite patchbay routing are not available to you. But for mastering purposes, this is usually irrelevant.

## Conclusion / Up Next

This concludes *Part 1* of this series of posts. You should now be able to run your new Arch Linux install in `realtime` mode, achieving incredible latencies without risking system stability. And you should even be able to run your Windows VSTs within your Arch system by utilizing `wine-staging`!

In *Part 2* of this series, I'll demonstrate how to manage your audio signal flows, custom routing, plugin states, and more. We'll also introduce the concepts of MIDI and OSC communication with `jack2` and the ALSA framework so that you can use your interface hardware to interact with Ardour5, non-daw, etc.

All of the musical and audio engineering work I do originates from my Linux audio systems. Check out what I've achieved using only free and open source software, at the [Sonic Multiplicities homepage](https://multipli.city). You can hear many of my mastering projects at [my old company's SoundCloud account](https://soundcloud.com/sacreddata).
