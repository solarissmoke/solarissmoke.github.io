---
layout: post
title: Akismet Bloat
---
<p>While working on the latest release of <a href="http://wordpress.org/extend/plugins/wp-conditional-captcha/">Conditional CAPTCHA for WordPress</a>, I realised that <a href="http://wordpress.org/extend/plugins/akismet/" rel="nofollow">Akismet</a> stores a whole bunch of metadata for every comment, which can seriously bloat your WordPress database.
<p>For each comment, Akismet records whether or not the comment was flagged as spam. Then, it stores a comment history which logs every change that is made to the status of the comment (spam, pending, approved etc), along with details of user who made it. This metadata is never deleted! At the very least, it means one extra row of metadata per comment (more for spam comments that are later approved).
<p>I don’t find this history particularly useful — certainly not so much that it needs to be kept forever. Here’s what I use to prevent Akismet from storing this metadata:
<pre><code>function prevent_akismet_history( $check, $object_id, $meta_key ) {
    $to_filter = array( 'akismet_result', 'akismet_history', 'akismet_user', 'akismet_user_result' );
    if( in_array( $meta_key, $to_filter ) )
        return false;
    return $check;
}

add_filter( 'add_comment_metadata', 'prevent_akismet_history', 10, 3 );
</code></pre>
<p>I’ve also added an option to <em>Conditional CAPTCHA</em> to do this automatically.
<p>To delete existing metadata, the following SQL query would work:
<pre><code>DELETE FROM `$wpdb->commentmeta` WHERE `meta_key` 
    IN ( 'akismet_result', 'akismet_history', 'akismet_user', 'akismet_user_result' );</code></pre>