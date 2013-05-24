---
comments: true
date: 2013-05-20
layout: post
title: 502 Bad Gateway on nginx Using Node on Elastic Beanstalk
summary: I started to work with Elastic Beanstalk on AWS recently and ran into an issue when trying to deploy a Node.js app that I thought might be worth sharing.  I created a test Node.js and Express application locally that worked fine and I then tried deploying using the Elastic Beanstalk console.  Everything appeared to deploy correctly but when I tried accessing my url I received the following error "502 Bad Gateway on nginx/1.2.6"...
summary_horizontal_position: left
summary_vertical_position: 10
additional_title_css: small-title
image: london.jpg
image_credit_name: Anirudh Koul
image_credit_url: http://www.flickr.com/photos/anirudhkoul/3499471010/
tags: ["Node", "AWS", "Elastic Beanstalk", "Nginx"]
---

I started to work with [Elastic Beanstalk](http://aws.amazon.com/elasticbeanstalk/) on [AWS](http://aws.amazon.com) recently and ran into an issue when trying to deploy a Node.js app that I thought might be worth sharing.  I created a test Node.js and Express application locally that worked fine and I then tried deploying using the Elastic Beanstalk console.  Everything appeared to deploy correctly but when I tried accessing my url I received the following error "502 Bad Gateway" on "nginx/1.2.6".  

<img src="/images/posts/badgateway.png" alt="bad gateway" title="bad gateway" />

Unfortunately this error message didn't give me much to go on, but when I looked in the Elastic Beanstalk log files I saw the following entry:
<br/><br/>

<pre>
2013/05/18 13:33:37 [error] 1708#0: *11 connect() failed (111: Connection refused) while connecting to upstream, client: 10.159.27.244, server: , request: "GET / HTTP/1.1", upstream: "http://127.0.0.1:8081/", host: "fitfavorites.elasticbeanstalk.com"
</pre>
<br/>

The confusing part of this error message for me was the port number of 8081, that was not the port I was expecting Beanstalk to utilize for my site.  Looking at the code that was working locally I realized I had hard coded the port number to 3000.  I quickly modified my configuration code with the following snippet.  This code allows the application to use either a port setting set in the environment variables or default to 3000 if no port is configured.
<br/><br/>

<pre>
var express = require('express');
var app = express();

app.configure(function(){<br/>
  app.set('port', process.env.PORT || 3000);
  ....
</pre>
<br/>

After re-deploying this to AWS, sure enough my problem disappeared. The one thing that was of interest to me was that I did not have to set an environment variable, it appears that Beanstalk has this set already for me.  

I hope this helps anyone else who runs into this same issue.  Have fun with Node on Elastic Beanstalk!


