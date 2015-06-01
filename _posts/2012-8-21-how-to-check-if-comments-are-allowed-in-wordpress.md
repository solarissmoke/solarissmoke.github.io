---
layout: post
title: How to check if comments are allowed in WordPress
---
<p>There are two ways that plugin and theme authors can check whether comments are allowed on a post in WordPress:
<p>Old-school way (wrong unless you have a very good reason):
<pre><code>if( $post->comment_status == 'open' ) {
	// do something here
}
</code></pre>
<p>Correct way:
<pre><code>if( comments_open() ) {
	// do something here
}
</code></pre>
<p>The second way is better because the <a href="http://codex.wordpress.org/Function_Reference/comments_open" rel="nofollow"><code>comments_open()</code></a> function is filterable — other plugins and themes can alter its output depending on context.
<p>What makes WordPress so powerful is the underlying API. Plugin and theme authors can modify almost anything, and this is why there are so many thousands of plugins and themes out there. But we plugin and theme authors need to make sure we return the favour, so that our themes/plugins can play nicely with the rest. So, if your plugin or theme is using the first method above, please change it to use the second.
<p>One other note for developers: WordPress has for some time now supported arbitrary post types (not just the posts and pages that we’re all used to), some of which don’t necessarily support comments at all. So it’s a good idea to check whether a post type even supports comments before running any comment-specific code:
<pre><code>if( post_type_supports( get_post_type(), 'comments' ) ) {
	// put comment related code here, including...
	if( comments_open() ) {
		// ...a comment form, maybe
	}
}
else {
	// sit back and relax
}
</code></pre>

<p>The <a href="http://core.svn.wordpress.org/tags/3.4.1/wp-content/themes/twentyeleven/comments.php" rel="nofollow">code</a> <a href="http://core.svn.wordpress.org/tags/3.4.1/wp-content/themes/twentyeleven/content.php" rel="nofollow">beneath</a> the default WordPress theme uses both of the functions mentioned above.