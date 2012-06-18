---
layout: post
title: "Dynamic URLs with Amazon S3's Static Website Hosting"
date: 2012-06-16 14:33
comments: true
categories: 
published: false
---

If you want to host a static website, Amazon S3's [Website Hosting](http://docs.amazonwebservices.com/AmazonS3/latest/dev/WebsiteHosting.html) is a very cheap and affordable way. "Static" hosting may seem limited, but with Javascript and HTML5 becoming more powerful, you can accomplish quite a bit without a traditional web server.

If you aren't familiar with S3, the basic steps are:

1. create a Bucket, which acts as a sort of directory of files
1. upload files to your bucket, optionally making them public
1. enable Website Hosting, providing an index and error page
1. Amazon gives you a domain which serves your Bucket, which you can
   then create a CNAME to from any of your domains.

Now, for very literally pennies a month, you
have 99.99% uptime and 99.99999999% durability on your files, fast
bandwidth, all without a single server or process to worry about.

This can work well for a traditional static site, but what if you'd like
to have nice, dynamic URLs? Let's create a simple example of
bgcolor.com. You want users to be able to specify the desired background
color via the URL. You could use query strings (bgcolor.com/?blue) or hashes (bgcolor.com/#blue) to
pass data to Javascript on your index page, but this isn't likely to be
understandable or easy to memorize to the average non-technical person.

Enter S3's "error" page.
