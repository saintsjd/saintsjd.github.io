---
layout: post
title: ituprising.com is now saintsjd.com
permalink: /2010/09/ituprising-com-is-now-saintsjd-com/index.html
tags: [ books, life, open source ]
---

These days I spend more time talking to people than I do computers. I am enjoying it greatly. To reflect these changes I have moved my blog from ituprising.com to saintsjd.com.

To accomplish this and keep all of my URLs working properly I used Apache mod_rewrite

{% highlight apache %}

Options +FollowSymLinks
RewriteEngine on
RewriteCond %{HTTP_HOST} ^([^.:]+\.)*ituprising\.com\.?(:[0-9]*)?$ [NC]
RewriteRule ^(.*)$ http://www.saintsjd.com/$1 [R=301,L]

{% endhighlight %}