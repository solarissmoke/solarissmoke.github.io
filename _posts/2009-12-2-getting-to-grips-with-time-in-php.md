---
layout: post
title: Getting to grips with time in PHP
---
<p>Working with time-sensitive code in PHP can get a little confusing (for me at least) when you have to deal with calculations in up to three different time zones: the server time zone, <abbr title="Greenwich Mean Time">GMT</abbr> or <abbr title="Temps Universel Coordonné, or Coordinated Universal Time">UTC</abbr>, and the client time zone. Here's a little table with the most important time functions in PHP, that seeks to help clarify what they do.
<p>The server time zone used for these examples is <strong>GMT+1</strong>. The Unix epoch is defined as Thu, 01 Jan 1970 00:00:00 GMT (and is therefore a fixed point in time, regardless of local time zone).
<table><thead>
<tr><th>Function<th>Output<th>Comments
<tbody>
<tr><td><code>time()</code><br /><small>(run on Tue, 01 Dec 2009 00:00:00, server time)</small><td>1259622000<td>The number of seconds between the Unix epoch and the run-time of the function.
<tr><td><code>mktime(0,0,0,12,1,2009)</code><td>1259622000<td>The number of seconds between the Unix epoch and the input time (specified in the server time zone).
<tr><td><code>gmmktime(0,0,0,12,1,2009)</code><td>1259625600<td>The number of seconds between the Unix epoch and the input time (specified in GMT).
<tr><td><code>strtotime('Tue, 01 Dec 2009 00:00:00')</code><td>1259622000<td>Without a timezone specified in the input string, the function assumes it is in the server time zone.
<tr><td><code>strtotime('Tue, 01 Dec 2009 00:00:00 GMT')</code><td>1259625600<td>With a timezone specified in the input string, the function will use that time zone instead of the server time zone.
<tr><td><code>date('D, d M Y H:i:s', 1259622000)</code><td>Tue, 01 Dec 2009 00:00:00<td>Takes a Unix timestamp (by definition in GMT) and returns a formatted date in the server time zone.
<tr><td><code>gmdate('D, d M Y H:i:s', 1259622000)</code><td>Mon, 30 Nov 2009 23:00:00<td>Takes a Unix timestamp (by definition in GMT) and returns a formatted date in GMT.
</table>
<p>The Unix timestamps used above translate to the following GMT times:
<ul><li>1259622000: Mon, 30 Nov 2009 23:00:00 GMT<li>1259625600: Tue, 01 Dec 2009 00:00:00 GMT</ul>
<p>When dealing with time-related calculations, I strongly recommend doing all the number-crunching in GMT, and translating this to client/server time afterwards, otherwise you seriously risk getting confused (especially if you have things like daylight savings to contend with as well).
<p>The <code>date_default_timezone_set</code> function can be used to specify your own time zone for PHP to use (overriding the server time zone) - I find it is easiest to set this to GMT, thereby eliminating the complexity introduced by the server time zone.
<p><small>PHP function references: <a href="http://www.php.net/manual/en/function.time.php">time</a>, <a href="http://www.php.net/manual/en/function.mktime.php">mktime</a>, <a href="http://www.php.net/manual/en/function.gmmktime.php">gmmktime</a>, <a href="http://www.php.net/manual/en/function.strtotime.php">strtotime</a>, <a href="http://www.php.net/manual/en/function.date.php">date</a>, <a href="http://www.php.net/manual/en/function.gmdate.php">gmdate</a>, <a href="http://www.php.net/manual/en/function.date-default-timezone-set.php">date_default_timezone_set</a></small>