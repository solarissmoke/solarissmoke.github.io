---
layout: post
title: Cleaning up failed multi-part uploads on Amazon S3
---
<p>I’ve recently been using <a href="http://s3tools.org/s3cmd">sc3cmd</a> to back up a lot of data to Amazon S3. Version 1.1.0 (currently in beta) supports multi-part uploads. It has borked a few times half way through large uploads, without properly aborting the operation server-side. This meant that the parts uploaded so far were not removed from the server, and that’s bad because Amazon <a href="http://docs.amazonwebservices.com/AmazonS3/latest/dev/mpuoverview.html#mpuploadpricing">charges for this storage</a>.

<p>s3cmd doesn’t currently have any way to list or abort interrupted multi-part uploads, which meant I had to figure out some other way to do it. It turned out to be quite simple using Python and the <a href="http://code.google.com/p/boto/">boto</a> library:

<pre><code>from boto.s3.connection import S3Connection
connection = S3Connection("your_access_key", "your_secret_key")
bucket = connection.get_bucket("your_s3_bucket_name")
uploads = bucket.get_all_multipart_uploads()
print len(uploads), "incomplete multi-part uploads found."
for u in uploads:
	u.cancel_upload()
# If another client is in the process of uploading, then it won't have been cancelled
uploads = bucket.get_all_multipart_uploads()
if len(bucket) > 0
	print "Warning: incomplete uploads still exist."
</code></pre>