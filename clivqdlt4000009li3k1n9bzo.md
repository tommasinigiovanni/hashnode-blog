---
title: "Merging Video and Audio"
seoTitle: "Merging Video and Audio with FFmpeg: A Step-by-Step Guide"
seoDescription: "Discover how to easily merge a video file and an audio file using FFmpeg. Follow our detailed step-by-step guide to solve your media issues in just a few mi"
datePublished: Wed Jun 14 2023 13:11:24 GMT+0000 (Coordinated Universal Time)
cuid: clivqdlt4000009li3k1n9bzo
slug: merging-video-and-audio
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/yk9VXp4W5-Q/upload/60d2739de7a8b488ca722ebda5787586.jpeg
tags: video-editing-app, merging-audio-video

---

# **The problem**

It's a situation many of us have found ourselves in - you have two separate files, one with the video content you want, and the other containing the corresponding audio. Perhaps you've downloaded a silent film and a separate commentary track, or you've recorded a presentation and the audio separately. Whatever the case may be, you're now faced with the task of merging these two into a single, cohesive file. In this blog post, we'll explore how to do exactly that using the FFmpeg tool.

# **The analysis**

FFmpeg is a free and open-source software that allows you to manipulate multimedia files. It can convert audio and video into almost any format, and it is widely used for video and audio streaming.

# **The solution**

The command we will be using today is as follows:

```bash
ffmpeg -i video.mp4 -i audio.mp4 -c:v copy -c:a aac -map 0:v:0 -map 1:a:0 output.mp4
```

Now, let's do a detailed analysis of this command:

1. `ffmpeg`: This is the command that starts the FFmpeg program.
    
2. `-i video.mp4 -i audio.mp4`: These are the input files. The first `-i video.mp4` tells FFmpeg that `video.mp4` is our input video file, while the second `-i audio.mp4` tells FFmpeg that `audio.mp4` is our input audio file.
    
3. `-c:v copy -c:a aac`: These options define the codecs we want to use. `-c:v copy` tells FFmpeg to copy the original video codec. `-c:a aac` tells FFmpeg to use the AAC audio codec for the output file.
    
4. `-map 0:v:0 -map 1:a:0`: These commands map the input streams to the output file. `-map 0:v:0` tells FFmpeg to take the first video stream (v:0) from the first input file (0) and `-map 1:a:0` tells FFmpeg to take the first audio stream (a:0) from the second input file (1).
    
5. `output.mp4`: This is the name of the output file. The resulting file will be a .mp4 file with the video from the first input file and the audio from the second input file.
    

And there you have it! With a single command, you can merge a video with an audio. I hope this post has been helpful. If you have any questions or comments, don't hesitate to leave them below.

Cheers!