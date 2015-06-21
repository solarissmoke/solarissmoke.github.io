---
layout: post
title: 'Incapsula does not obey "Cache-Control: private" directives'
---

[We](https://regulusweb.com) use Incapsula as a reverse proxy for some of our sites, and recently ran into an issue with what appears to be a wilful violation of the HTTP specification by Incapsula.

Incapsula's [documentation](https://incapsula.zendesk.com/hc/en-us/articles/200627510-How-to-control-the-way-my-site-is-cached) says the following about how it handles Cache-Control headers:

<blockquote>
<p><strong>Cache-Control</strong> - if it's set to no-cache / private / must-revalidate / proxy-revalidate/ no-store – then it'll prevent caching by Incapsula, unless site Acceleration Mode is set to Aggressive.</p>
</blockquote>

Unfortunately this isn't the case. The following response headers from an origin server:

<div>
<code><pre>
    Cache-Control: max-age=86400, private
    Content-Encoding: gzip
    Content-Length: 2431
    Content-Type: text/html; charset=utf-8
    ...
</pre></code>
</div>

Get converted into this by Incapsula's proxy:

<div>
<code><pre>
    Cache-Control: max-age=86398, public
    Content-Encoding: gzip
    Content-Length: 2431
    Content-Type: text/html; charset=utf-8
    ...
</pre></code>
</div>

This is a problem because what was meant to be a <strong>private</strong> resource has been cached and made <strong>public</strong> by Incapsula.

We contacted Incapsula to report this issue. This was the explanation we received:

<blockquote>
<p>private: Indicates that all or part of the response message is intended for a single user and MUST NOT be cached by a shared cache.</p>
<p>The max-age directive on a response implies that the response is cacheable (i.e., "public") unless some other, more restrictive cache directive is also present.</p>
<p>Since Incapsula is a CDN, which considers as a shared cache, the value of max-age=NUMBER and Cache-Control=private contradict each other and must not configured in this way.</p>
</blockquote>

The statement about <code>max-age</code> implying that a resource is public is false. The [HTTP specification](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9.1) explicitly allows private resources to have a max-age:

<blockquote>
<p>private</p>
<p>Indicates that all or part of the response message is intended for a single user and MUST NOT be cached by a shared cache. This allows an origin server to state that the specified parts of the response are intended for only one user and are not a valid response for requests by other users. <strong>A private (non-shared) cache MAY cache the response.</strong></p>
</blockquote>

The correct behaviour would be for Incapsula to give the <code>private</code> directive precendence over <code>max-age</code> (rather than the other way around). Instead it takes the rather dangerous step of simply removing the <code>private</code> directive!

In any case, the point is that Incapsula will violate a <code>Cache-Control: private</code> directive unless you also set <code>max-age=0</code>.