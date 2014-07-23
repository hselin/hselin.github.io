---
layout: post
title: "iOS, XMLHttpRequest and Video"
date: 2014-07-23 14:30:33 -0700
comments: true
categories: 
---

<div style="overflow:auto">
  <img class="float-left" src="/images/posts/apple-logo.png" style="width: 200px; height: 100px; margin-right: 10px">
  So... Safari on iOS <a href="http://caniuse.com/video ">supports</a> HTML5 video element. That's great, good job Apple.
  <br />
  BUT - why can't you take the extra step of allowing the video element to work with blob object URL as the source?!

{% codeblock lang:js %}
  var oReq = new XMLHttpRequest();
  oReq.open("GET", URL, true);
  oReq.responseType = "blob";

  oReq.onreadystatechange = function() {
    if((oReq.readyState == 4) && (oReq.status == 200)) {
      var blobURL = (window.URL || window.webkitURL).createObjectURL(oReq.response);
      $("#myVideo").attr("src", blobURL);
    }
  }

  oReq.send();
{% endcodeblock %}
  
</div>