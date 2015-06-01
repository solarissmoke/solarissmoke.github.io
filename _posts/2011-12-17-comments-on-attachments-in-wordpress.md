---
layout: post
title: Comments on attachments in WordPress
---
<p>The way WordPress handles commenting on attachments (media) is a bit flaky. The comment status of attachment pages is set to the global default comment status when the media item is attached to a post. After that, there’s no way to change it! Surprising? Yes. There are a couple of truly ancient <a href="http://core.trac.wordpress.org/ticket/8177">bug</a> <a href="http://core.trac.wordpress.org/ticket/9839">reports</a> related to this, but they’ve received very little attention.

<p>I ran into this issue when working on some of my comment-related plugins. So here’s how to fix it.

<h2>If you’re lazy</h2>
<p>If you want to just disable comments on all media items, use the <a href="http://wordpress.org/extend/plugins/disable-comments/">Disable Comments</a> plugin.
<p>If you want media items to inherit their comment status from their parent post, use the <a href="http://wordpress.org/extend/plugins/comment-control/">Comment Control</a> plugin.

<h2>If you’re bored :)</h2>
<p>To disable comments on all media items, put something like this in your theme’s <code>functions.php</code> file:
<pre><code>function filter_media_comment_status( $open, $post_id ) {
	$post = get_post( $post_id );
	if( $post->post_type == 'attachment' ) {
		return false;
	}
	return $open;
}

add_filter( 'comments_open', 'filter_media_comment_status', 10 , 2 );
</code></pre>

<p>If you want media items to inherit their comment status from their parent post:
<pre><code>function filter_media_comment_status( $open, $post_id ) {
	$post = get_post( $post_id );
	if( $post->post_type == 'attachment' && $post->post_parent ) {
		return comments_open( $post->post_parent );
	}
	return $open;
}

add_filter( 'comments_open', 'filter_media_comment_status', 10 , 2 );
</code></pre>