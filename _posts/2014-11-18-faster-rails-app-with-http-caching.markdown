---
layout: post
title:  "Faster Rails applications with HTTP caching"
date:   2014-11-18 18:30:12
categories: ruby
---

Web application performance is one of the most important topic nowadays. Most of us create application that have to be fast not only on desktop but also on mobile devices. Mobile network is slow and this causes that we have to think about performance constanly. One of technique that we can apply in our application to make it faster is HTTP caching. I'm going to show you how we can use this technique in Rails application. 

## HTTP Headers

Browser and server are really talkative and they like to share knowledge about web pages and other resources. During every request and response, browser and server exchange data as HTTP headers. HTTP header contains the name of the header and its value (string). The easiest way to check the headers and theirs values is opening the developer console in a browser  (in Chrome you can find them in Network -> Headers tab):

<figure>
	<img src="/images/http-caching/http-headers.png" alt="HTTP headers in the developer console">
</figure> 


The headers which are interesting for us are:

* Last-Modified (response header) and If-Modified-Since (request header)
* Etag (response header) and If-None-Match (request header)
* Cache-Control (response header)

Let's look at them separetly.

### Last-Modified/If-Modified-Since

Let's say that browser asks server for page first time (we never visit the page before). Server sends the response (for example generated HTML page) with status 200 and it can also set HTTP header: Last-Modified. This header should contain the date of last modification of the page:

<figure>
	<img src="/images/http-caching/last-modified.png" alt="the example of Last-Modified HTTP header">
</figure> 

When browser finds this header in the response, it gets the header's value and caches it. When the browser asks server second time for the same page, it sends this cached value in the HTTP header: If-Modified-Since

<figure>
	<img src="/images/http-caching/if-modified-since.png" alt="the example of If-Modified-Since HTTP header">
</figure> 

This way browser asks the server: "Is the page being modified since the last time I saw it?". When server finds the If-Modified-Since header in the request, it can compare the value from the header with last modified value for the page. If they are the same, the server sets response status to 304 and it sends to browser empty response body. When the browser gets reponse with this status, it renders the page from cache.

What are the adventages of this behaviour? First and foremost, we decrease the response bandwidth, as with 304 status, server sends to browser empty body in response. This, of course, makes page rendering faster. The second adventage is that we control browser cache. The third is that we might reduce view rendering on the server side, but this is not the default behaviour that Rails provides us. We have to do some settings by ourselves. I'll talk about later in this article.

As always there are also disadventages: the browser can't decide without server that it can take page from cache. So the browser always have to make request to server, which, again, takes time.  

### ETag/If-None-Match









