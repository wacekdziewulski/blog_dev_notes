---
layout: post
title:  "AWS: Static website hosting - explained part 1/4"
date:   2019-10-16 09:30:00 +0100
categories:
- aws
---

I was using a VPS to serve all of my websites for more than a few years. Since most of them were running on WordPress, I could manage the database, SSL Certificates (Let's Encrypt) and Apache on my own. While this may have made sense a couple of years back, a single author blog does not need Wordpress underneath. If You're a saavy tech person, and since You're here - You probably are, then chances are You could do well with a static website.

I'm using Jekyll for all of my stuff now and it's a tremendous tool once You get the grasp of that. And apart from maybe having to use HTML or Markdown to create the content, it's not a big deal to have it running in no time. I won't be posting about that however, because the application's website is more than enough to get You started: (Jekyll)[https://jekyllrb.com/].

So Jekyll creates a static website, which may be uploaded to any hosting service or put in Your favourite HTTP server's website directory. Since the world is switching to serverless, I decided to go with the flow and chose AWS, which I'm a bit familiar with, to set up all of my blogs. It was meant to be simple, but there are quite a few obstacles on the way. This blog post is all about getting round them.

# How is it all structured

We'll be using quite a few AWS services to address the hosting needs:
- __S3 Bucket__ - store and serve the actual website
- __CloudFront__ - manage HTTPS and act as a CDN
- __Lambda__ - rewrite URLs, specifically append index.html, when needed
- __Route 53__ - redirect Your domain name to CloudFront

Through the article I'll be discussing why we need them, and how to make them running.

# Host website in an S3 bucket

The first step is actually pretty simple. We have to upload the static website to our newly created S3 Bucket and enable hosting.
During bucket creation, we should mark the data as public, as the website's resources have to be available to be served by S3.

![]({{'assets/aws/website-hosting/s3_bucket_website.png' | relative_url}})

Once that's done, let's go the the `Properties` tab and select `Static website hosting`. Select `Use this bucket to host a website` to enable hosting. AWS automatically creates an endpoint for accessing it through the browser. It's highly recommended to create an S3 bucket named the same as the target domain we will be using. Since S3 bucket hosting is pretty dumb, it won't automatically redirect to index.html, when we access the endpoint's address. This has to be specified in the "Index document", so if that's the main entry point to Your website then type it here. If You're using, say, index.php - type that in instead. You know the drill.

![]({{'assets/aws/website-hosting/s3_bucket_hosting.png' | relative_url}})

Let's now go the the `Permissions` tab and first check that the public access is unlocked.

![]({{'assets/aws/website-hosting/s3_block_public_access.png' | relative_url}})

Finally, since the content will be served by CloudFront at some point (more on that later), we will need to add a simple policy for other services to be able to access our S3 bucket objects. The policy is as follows:

{% highlight javascript %}
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::wacekdziewulski.ovh/*"
        }
    ]
}
{% endhighlight %}

![]({{'assets/aws/website-hosting/s3_bucket_policy.png' | relative_url}})

All these steps should allow us to access the website using HTTP(!) on the URL, which was shown under `Static website hosting`. That's good news!

Since the world has switched to HTTPS now however, we will have to create an SSL certificate and have the resources served using this protocol.

[Continue to the next entry.]()
