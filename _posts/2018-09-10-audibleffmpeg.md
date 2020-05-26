---
layout: post
title:  "Playing Your Audible Audiobooks In Linux"
date:   2018-09-10 10:44:00

categories: ffmpeg audible audiobooks linux foss
---
If you want to convert an Audible .aax file into a less restrictive file format, you can do so with ease using <a href="https://ffmpeg.org/">FFmpeg</a>, one of my all-time favorite GPL projects.

Navigate to the directory where the .aax file is that you’d like to test on, and perform the following on the command line:

```
ffmpeg -activation_bytes 1CEB00DA -i test.aax -vn -c:a copy output.mp4
```

Doing this creates an mp4 container with only an AAC audio stream inside. By adding the `-c:a copy` argument, we’re telling FFmpeg to do a stream copy of the audio, negating the need to compress and process the audio again. Since Audible audio is encoded at 64kbps HE-AAC or lower, this argument is essential to retain quality!

This MP4 can now be played on any conventional audiovisual playback software that supports the MP4 container file. Enjoy!
