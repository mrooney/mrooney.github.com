---
layout: post
title: "DNS: Root/Apex CNAMES in Practice"
date: 2012-05-01 19:56
comments: true
categories: 
---

While there's technically a limitation in the DNS specification that
says domain apexes need to be A records, it is often desirable to be
able to point it at another domain, and I wanted to break the common
misconception that this can't be accomplished.

The simplest case is that you have a "naked" domain such as example.com
that you want to forward to www.example.com. While you can't use a CNAME
for your apex, you can indeed accomplish this forward in practice. I
don't mean to imply that there's a magical way to have a CNAME for
an apex (though with the last AWS method, there kind of is), simply
that what you want to achieve is doable without pointing your apex
directly at application web servers.

The Easy Way
============

If all your want is to have example.com go to www.example.com, there's
a free service called [wwwizer](http://wwwizer.com/naked-domain-redirect) that does this for you. Simply set your
apex A record to 174.129.25.170. That's it! It's really that simple!

The Slightly Harder Way
==============

You can do what wwwizer is doing yourself, and handle more cases than an
apex going to www, so let's look at how that works. If you curl a domain
using [wwwizer](http://wwwizer.com/naked-domain-redirect), you'll get an HTTP 301 to www.thatdomain.com. So all that
wwwizer is doing is using the host variable you send it when you request
thatdomain.com, and doing a 301 redirect to www.thatdomain.com.

Here's an example nginx server directive that accomplishes this in a
generic fashion:

    server {
        listen 80;
        server_name _;
        return 301 http://www.$http_host$request_uri;
    }

If you want more than a simple apex to www forward, you just need to
create a map of the domains to the desired CNAME, and you are good to
go. You can even still use DNS for this, so you have a single source of
truth, by using [DNS as a key-value store](http://webpulp.tv/episodes/learn-how-tropo-built-a-huge-voice-cloud-while-using-dns-for-a-key-value-store).

All you need to do is point your apex at an IP (or set of IPs) that have
a server configured to perform the appropriate 301s. At this point you
might be complaining that having to maintain a static IP is what you
were trying to avoid, and that's fair. But it is important to note that
the A record(s) you use for your apex don't have to run your
application, so you can simply throw nginx/apache on a service you
already keep highly available on a static IP (puppetmaster, internal DNS servers, et
cetera). This allows you to use a perhaps more stable and less ephemeral
set of IPs than web server IPs. Plus, multiple domains / applications
can share this server. And remember, if you just need naked ->
www redirection, just use wwwizer instead of worrying about this
yourself.

The Amazon Way
==============

If you are using AWS, [they support mapping a zone apex to an Elastic
Load Balancer](http://aws.amazon.com/route53/faqs/#Zone_apex) (ELB) via
their DNS provider Route 53. So this means even as your load balancer is
transparently replaced or scaled up and down, you don't have to worry
about the IPs involved.
