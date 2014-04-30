---
layout: post
title: "WebRTC Introduction"
date: 2014-05-30 02:06:29
categories: computer
---

What is WebRTC ? WebRTC is an Industry and standards effort to put real-time communciations cappabilites into all browser and make these capabilities APIs (Johnston - API WebRTC). Real Time Communication on WebRTC enables peer to peer video, audio, data communication between web browsers. This allows for video calling, video chat, and peer to peer file sharing.

Why You Should use WebRTC ? Because WebRTC require No plugins, applications, all you need is a WebRTC compatible browser. As Developer, WebRTC is entirely peer to peer, so you don't have to pay any of the bandwitch accross the wire.

Jason Uberti Google, one of the Industry's most visible advocates of WebRTC, says that technology is generating excitement among players accross the communication spectrum. This includes Cisco, Microsoft, Ericsson, and many others. He says this is becasue 'the premise of webRTC is to build real time communication into the fabric of the web, where every browser has a built-in, state-of-art communication stack'

So, You can check on http://iswebrtcreadyyet.com/ to knowing browser ready used..

WebRTC has four API's to stream between browsers :

1. GetUserMedia()

The getUserMedia API provides access to multimedia streams (video, audio or both) form local devices.

Demo

{% highlight javascript %}
.........................
  navigator.getUserMedia = navigator.getUserMedia ||
  navigator.webkitGetUserMedia || navigator.mozGetUserMedia;
  var constrains = {audio: true, video: true}
  var video = document.querySelector("video");

  function successCallback(stream){
    console.log(stream);
    window.stream = stream;
    if(window.URL){
      video.src = window.URL.createObjectURL(stream);
    }else {
      video.src - stream;
    }
  }
  function errorCallback(error){
    console.log("navigator.getUserMedia error ", error);
  }

  navigator.getUserMedia(constrains, successCallback, errorCallback);
  var constrains = {audio: true, video: true}
  var video = document.querySelector("video");

  function successCallback(stream){
   console.log(stream);
   window.stream = stream;
   if(window.URL){
     video.src = window.URL.createObjectURL(stream);
   }else {
     video.src - stream;
   }

  function errorCallback(error){
    console.log("navigator.getUserMedia error ", error);
  }

  navigator.getUserMedia(constrains, successCallback, errorCallback);
  navigator.getUserMedia(constrains, successCallback, errorCallback);

{% endhighlight %}


Continued ... next time
