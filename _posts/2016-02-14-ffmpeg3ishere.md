---
layout: post
title:  "FFmpeg 3.0 Is Here"
date:   2016-02-15 4:11:00

categories: ffmpeg aac fdk-aac cenc
---
The team behind FFmpeg unveiled a major new release, “Einstein,” recently, and in the release notes appears a strongly-worded suggestion:

`We strongly recommend users, distributors, and system integrators to upgrade unless they use current git master.`

(By the way, if you’re thinking that today may be the day to look into FFmpeg’s git master – don’t. Seriously, just don’t. This is no slight against the FFmpeg team - it’s just that with a project of this nature, you have to build the nightly release every day to get helpful suppo
rt. Developers are committing small patches to individual subsets of the FFmpeg project every day, which is why the release candidates are a sure bet for most folks.)

There are many of you out there that run FFmpeg in production, so the team’s urgency to get users to update should be taken as an indication that big changes have come. Here’s some noteworthy entries in 3.0’s <a href="https://git.videolan.org/?p=ffmpeg.git;a=blob_plain;f=Changelog
;hb=n3.0">release notes</a>:</p>

<div class="highlighter-rouge"><pre class="highlight"><code># This feature will allow content enterprises to create encrypted proxies of their sensitive content
- Common Encryption (CENC) MP4 encoding and decoding support
# The DXV codec is used in live visual performances (often referred to as VJing)
- DXV decoding
# Stereo audio processing tools like these hint at optimizing audio tracks for headphone listeners
- extrastereo filter
- stereowiden filter
- stereotools filter
# FFmpeg's AAC improvements mean fewer patent/royalty issues when creating canonical MP4s
- extensive native AAC encoder improvements and removal of experimental flag
# AcousticID generation for audio tracks!
- chromaprint fingerprinting muxer
</code></pre>
</div>

Sheesh! And that’s not even the half of it. Suffice to say, this FFmpeg release is a significant one. Ahead, I will explore the above-listed features in detail.

<h2 id="common-encryption-support-for-mp4">Common Encryption support for MP4</h2>

This feature is not documented on FFmpeg.org’s <code class="highlighter-rouge">ffmpeg-all.html</code> page. After a quick trip to #ffmpeg on freenode, I discovered that no documentation currently exists for this feature except for on the command line:

<div class="highlighter-rouge"><pre class="highlight"><code>$ ffmpeg -h muxer=mov | grep -encryption_scheme

# -encryption_scheme &lt;string&gt;     E....... Configures the encryption scheme, allowed values are none, cenc-aes-ctr
</code></pre>
</div>

Though a helpful start, I have not yet gotten this feature to work.

<h2 id="chromaprint-support">Chromaprint Support</h2>

Chromaprint’s AcousticID is an audio fingerprinting library. It’s pretty cool that they’ve implemented support for this within FFmpeg! There are some caveats, however. To generate an AcousticID of an audio file in FFmpeg, that file must be a <code class="highlighter-rouge">single 
signed native-endian 16-bit raw audio stream</code>. Yikes!

<h2 id="native-aac-codec">Native AAC Codec</h2>

Word on the street was, at first, that the FFmpeg native aac codec is now outperforming the fdk-aac codec. I was dubious of this claim, and recently, a respected super mega-nerd over at Hydrogenaud.io released <a href="https://hydrogenaud.io/index.php/topic,111085/topicseen.html">his own thoughts</a> on the newly-improved codec. Needless to say, <em>if you are updating to 3.0, do not get rid of libfdk-aac!</em> The fdk-aac codec is still the only available FFmpeg aac library that truly supports VBR, and it still wins in the sound quality department.
