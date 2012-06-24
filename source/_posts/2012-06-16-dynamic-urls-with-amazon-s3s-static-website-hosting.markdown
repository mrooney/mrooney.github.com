---
layout: post
title: "Dynamic URLs with Amazon S3's Static Website Hosting"
date: 2012-06-23 21:11
comments: true
categories: 
---

If you want to host a static website, Amazon S3's [Website Hosting](http://docs.amazonwebservices.com/AmazonS3/latest/dev/WebsiteHosting.html) is a very cheap and affordable way. "Static" hosting may seem limited, but with Javascript and HTML5 capabilities, you can accomplish quite a bit without a traditional web server.

If you aren't familiar with S3, the basic steps are:

1. create a Bucket, which acts as a sort of directory of files,
1. upload files to your bucket, optionally making them public,
1. enable Website Hosting, providing an index and error page,
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
understandable or easy to memorize for the average non-technical person.

Enter S3's "error" page. The page you specify as your error page will be
served on 404s, and importantly, served at the 404 URL itself. So we can
set both our index and error pages to the same page, and examine the URL
with Javascript to perform the correct routing, loading, or action. For
our bgcolor.com example, we can now allow users to type
bgcolor.com/blue. Let's take a look:

``` javascript
document.body.style.background = window.location.pathname.split(1);
```

And you've got it! Note that this technique doesn't mean you lose the
ability to have an actual 404 page; you can have your URL logic be aware of an
invalid URL and update the page appropriately or redirect to a 404.html. Just Google "javascript url router" if you don't want to write your own, as there are quite a few mature looking ones that already exist.

Enjoy! This is the technique I'm using to host [zazen.rowk.com](http://zazen.rowk.com/), and it
costs me $0.01/month (yes, one penny).
