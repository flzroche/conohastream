# Conoha Stream

## ① Most Important!

This is most important!

1. Insert the following code in <head> of **index.html**.

   ``` html
   <meta name="robots" content="noindex,nofollow">
   ```

   Required to avoid crawling by Google's search engine.

   

2. Edit the robots.txt file directly under root.
   Specify the directories to avoid crawling as shown in the following image.

   <img src="https://i.imgur.com/pDvgM2o.png" />

## ② Embed HLS.js into index.html using CDN

``` html
<script src="https://cdn.jsdelivr.net/npm/hls.js@1"></script>
<!-- Or if you want the latest version from the main branch -->
<!-- <script src="https://cdn.jsdelivr.net/npm/hls.js@canary"></script> -->
<video id="video" controls autoplay playsinline></video>
<script>
  var video = document.getElementById('video');
  var videoSrc = 'https://yourDomain/stream/video/master.m3u8';
  //
  // First check for native browser HLS support
  //
  if (video.canPlayType('application/vnd.apple.mpegurl')) {
    video.src = videoSrc;
    //
    // If no native HLS support, check if HLS.js is supported
    //
  } else if (Hls.isSupported()) {
    var hls = new Hls();
    hls.loadSource(videoSrc);
    hls.attachMedia(video);
  }
</script>
```

Alternatively, you can git clone from the following URL.

https://github.com/video-dev/hls.js

## ③ Convert video to one for HLS

Install ffmpeg beforehand.

To automatically change the bit rate (picture quality) according to the communication environment of the viewer, create a 1Mbps and a 10Mbps version as shown below.

- 1Mbps

``` cmd
ffmpeg -i sample.mp4 -b:v 1M -c:a copy -f hls -hls_playlist_type vod -hls_time 5 -g 24 -hls_segment_filename "sample-1m%3d.ts" sample-1m.m3u8
```

- 10Mbps

``` cmd
ffmpeg -i sample.mp4 -b:v 10M -c:a copy -f hls -hls_playlist_type vod -hls_time 5 -g 24 -hls_segment_filename "sample-10m%3d.ts" sample-10m.m3u8
```



- When the ffmpeg process is finished, create a 1m directory and a 10m directory, and move the files into each of these directories and put them together.

``` cmd
mv sample-1m* 1m/ && mv sample-10* 10m/
```



- Create the master.m3u8 file yourself as follows.

``` html
#EXTM3U
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=1000000
1m/sample-1m.m3u8
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=10000000
10m/sample-10m.m3u8
```



## ④  Upload the files via SFTP, etc. and you are done!	

Finally, arrange each of them so that the tree structure looks like the following.

``` cmd
https://yourDomain/stream/

	video
	├── master.m3u8
	├── 10m
	│   ├── sample-10m.m3u8
	│   ├── sample-10m000.ts
	│   ├── sample-10m001.ts
	│   └── ...
	└── 1m
  	  ├── sample-1m.m3u8
   		├── sample-1m000.ts
   		├── sample-1m001.ts
   		└── ...

```



