---
layout: post
title:  "Working with RTSP in FFmpeg"
date:   2020-06-23 12:04:00
categories: media streaming foss cli linux cameras
---

Recently, I've found myself situated among a [great fireworks battle](https://news.yahoo.com/whats-sound-not-just-york-025006561.html?soc_src=hl-viewer&soc_trk=tw) in Brooklyn, NY. As we continue to work out the details about the heightened fireworks activity across the United States, I felt it was important that I document and share my experience to contribute what I can to the ongoing conversation. So, I grabbed an old IP camera I had in a corner gathering dust, placed it outside on my fire escape, and fired it up to get a decent shot of the night sky in my neighborhood.

## IP Camera Trickiness

![](/rtsp-control.jpg)

I did a little snooping around the control pannel software on the camera to discover that the RTSP protocol is used to transport the signal over ethernet. Being an FFmpeg stan, I was confident that I would be able to quickly grab and relay my [UVC-G3-DOME](https://store.ui.com/collections/unifi-protect-cameras/products/unifi-video-camera-g3-dome) signal right away. However, I failed to consider the contexts posed by the delivery transport. So, I was a bit surprised to discover that typing a typical `ffmpeg` test command to ingest this camera's feed resulted in some really weird error logs and a very corrupt video output.

```sh
ffmpeg -re -i rtsp://192.168.3.55:554/s0 -c:v libx264 -c:a libmp3lame test.mkv
```

![](/rtsp-corrupt.jpg)

The above command would be adequate for testing the feed from software like `obs` and from services like AWS Kinesis or Wowza Cloud. But this RTSP signal looked all sorts of weird when saved to disk.

## How To RTSP in FFmpeg

Upon doing some research, I discovered that `ffmpeg` has rich support for the [RTSP protocol](http://ffmpeg.org/ffmpeg-all.html#rtsp), but as a result there are some specific FFmpeg flags that must be utilized that I'd failed to include in my previous test command. `ffmpeg` requires the `-rtsp_transport` input flag to declare the transport used by the camera - TCP in my camera's case. This new test command produced a most agreeable output.

```sh
ffmpeg -re -rtsp_transport tcp -i rtsp://192.168.3.55:554/s0 -c:v libx264 -c:a libmp3lame test.mkv
```

![](/rtsp-clean.jpg)

### RTMP Relaying

My ultimate use case is a common one - I wanted to be able to record the live feed to local disk as well as relay the stream to a YouTube live stream RTMP endpoint. To accomplish this, `ffmpeg` requires some specific output options declared to ensure compliance, as the direct RTSP signal from the camera is not in a compliant format.

To resolve the RTMP compliance issue, I declared `-f flv -ar 44100 -r 30/1 -pix_fmt yuv420p` as output options for the RTMP portion of  my `ffmpeg` command. Declaring the format as FLV ensures the output bytestream is ordered in a way that is compliant with YouTube's RTMP endpoint. Setting an explicit 44.1kHz sampling frequency for our audio guarantees full-spectrum audio while lowering overall output bitrate. And finally, enforcing the YUV420P pixel format ensures that your camera's colors are correctly transformed to match YouTube's color space.

The full command now looks like this:

```sh
ffmpeg -re -rtsp_transport tcp -i rtsp://192.168.3.55:554/s0 -c:v copy \
-c:a copy stream_capture.mkv -c:v libx264 -b:v 3M -minrate:v 3M -maxrate:v 3M \
-bufsize:v 6M -g 120 -r 30/1 -x264-params keyint=120:min-keyint=60 \
-c:a libmp3lame -ar 44100 -pix_fmt yuv420p rtmp://YOUTUBE_RTMP_URL
```

![](/fireworks.gif)

That's definitely a firework exploding a few feet away from my apartment building! Nice.
