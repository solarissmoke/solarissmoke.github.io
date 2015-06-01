---
layout: post
title: CloudFlare alters conditional requests from web crawlers
---
<p>I’ve been using <a href="http://cloudflare.com" rel="nofollow">CloudFlare</a> on this site for quite a few months. On the whole I was quite happy with it — it certainly has filtered out a large chunk of visits from spambots and other nasties. There are some quirks however. Here are two conditional requests I made to my home page, along with the response headers (I’ve removed some headers that are not relevant):

<pre><samp>Request 1:

GET / HTTP/1.1
Host: rayofsolaris.net
User-Agent: Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)
If-Modified-Since: Thu, 02 Aug 2012 05:50:55 GMT
Cache-Control: max-age=0

Response 1:

HTTP/1.1 304 Not Modified
Server: cloudflare-nginx
Date: Fri, 17 Aug 2012 07:18:50 GMT
Cache-Control: max-age=604800
Expires: Thu, 09 Aug 2012 05:50:55 GMT
Last-Modified: Thu, 02 Aug 2012 05:50:55 GMT

* * *

Request 2:

GET / HTTP/1.1
Host: rayofsolaris.net
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; rv:14.0) Gecko/20100101 Firefox/14.0.1
If-Modified-Since: Thu, 02 Aug 2012 05:50:55 GMT
Cache-Control: max-age=0

Response 2:

HTTP/1.1 304 Not Modified
Server: cloudflare-nginx
Date: Fri, 17 Aug 2012 07:20:13 GMT
Cache-Control: max-age=604800
Expires: Thu, 09 Aug 2012 05:50:55 GMT
</samp></pre>

<p>The only difference between the two requests was the <code>User-Agent</code> header. In the first request I spoofed Googlebot’s user agent.
<p>Nothing out of the ordinary here. Both requests received identical <code>304 Not Modified</code> responses from the CloudFlare proxy. But the weird bit is that my web server (the one that the CloudFlare proxy connects to on behalf of the client), saw very different requests. Here are the log entries for the two requests:

<pre><samp>Request 1:

[17/Aug/2012:08:18:50 +0100] "GET / HTTP/1.1" 200 1638 "-" 
	"Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)"

Request 2:

[17/Aug/2012:08:20:13 +0100] "GET / HTTP/1.1" 304 0 "-" 
	"Mozilla/5.0 (Windows NT 6.1; WOW64; rv:14.0) Gecko/20100101 Firefox/14.0.1"
</samp></pre>

<p>Notice that with first the request, my web server returned a <code>200</code> response - i.e., it delivered the entire content of the web page. With the second request, it delivered a <code>304</code> response (and no content). <em>In both cases, the client received a <code>304</code> response</em>. So the CloudFlare proxy is doing something funky in between.
<p>After some investigation, I found that <strong>when it sees a Googlebot (or other crawler) user agent, CloudFlare strips the <code>If-Modified-Since</code> header from the request before passing it on to the web server</strong>. So my server always receives a non-conditional request. It means that all crawler visits are given a full <code>200</code> response (even though that may not be what it receives from the CloudFlare proxy), and that my server is sending out a whole lot more data that it needs to. This is particularly wasteful for items that are fetched frequently, such as feeds being retrieved by Feedfetcher.
<p>I’m not sure why CloudFlare would be intercepting and modifying requests from crawlers in this way. I’ve asked, and will update this post with their response. 
<p>In any case, the point to note is that CloudFlare isn’t necessarily cutting the load on your server — it might actually be increasing it! In my own case, this behaviour actually wiped out any benefit gained from static content caching (in terms of data transfer and processor usage) in the last month, and I’ve stopped using CloudFlare because of that.
<p><strong>Edit</strong> (5<sup>th</sup> September 2012): No response from CloudFlare — the support ticket that I raised has been closed, twice, without anyone addressing it.
<p><strong>Edit</strong> (12<sup>th</sup> September 2012): After reopening the ticket, I’ve been told that the issue has been passed on to the engineering team to investigate, but have not been given an explanation of why this is happening, and whether it is supposed to happen (I’m pretty sure it’s a bug).