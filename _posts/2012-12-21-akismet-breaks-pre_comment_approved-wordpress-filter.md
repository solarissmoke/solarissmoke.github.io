---
layout: post
title: Akismet breaks pre_comment_approved WordPress filter
---
<p>Note to WordPress plugin developers: use the <code>pre_comment_approved</code> filter with caution if there is a chance that Akismet will be active on sites using your plugin.
<p>There is a <a href="https://core.trac.wordpress.org/ticket/9968">long</a> <a href="https://core.trac.wordpress.org/ticket/16245">standing</a> bug in WordPress, which is that a self-referential call to <code>remove_filter</code> breaks the filter — any functions that had been scheduled to run after the one being removed will not be called.
<p>Unfortunately, this is exactly what Akismet does in the <a href="http://plugins.trac.wordpress.org/browser/akismet/trunk/akismet.php?rev=638848#L199"><code>akismet_result_spam</code></a> function — it tries to remove its own hook. The result is that whenever Akismet identifies a comment as spam, any functions hooked to the <code>pre_comment_approved</code> hook with priority greater than the default (10), will silently fail to execute.
<p>As an aside, there are other, simpler ways to ensure that a function runs only once — static or class variables, for example.
<p><a href="http://wordpress.org/support/topic/akismet-breaks-the-pre_comment_approved-filter-for-other-plugins">Bug report</a> and <a href="http://plugins.trac.wordpress.org/ticket/1625">Trac ticket</a>.