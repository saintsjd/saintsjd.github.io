---
layout: post
title: What to Optimize
tags: [devops, tomnod, high scalability]
---

If you hang out with developers enough, you will hear this refrain:

"Premature optimization is the root of all evil"

The idea is don't spend time fixing problems that you don't have because you are more likely to make your code clumsy and more complicated than it needs to be. 

In my recent work on www.tomnod.com we were hit with a huge amount of traffic during the search for missing Malaysia airlines flight MH370. From that experience, I can tell you that if premature optimization is evil... 

"Optimizing under fire is only slightly more fun" 

The question becomes what design decisions, which foundations can I lay today, that will let me scale my app to 100x or 1000x the load it handles today. Here is what we learned scaling Tomnod in March of 2014.

It is never premature to optimize these things:

1.  **Create a Productive Error Page** The page should be static html and javascript. Make sure it never includes hits to the database. This static page lets you provide useful information to the hungry crowd banging on your app's front door and gives you breathing room. While that page is displayed you are free to do updates and scale the system behind the scenes. Consider it one of the top priority features of your system. Test it in your releases.  Error pages can be useful. An email signup form on our error page (which posted to our SendGrid mailing list) collected email addresses and twitter followers who wanted to be updated as soon as the site was back up and usable. The amount of people who sign up on the error page when our site was "down" amounted to 3x what our entire database of contacts had been before. This made for highly productive downtime and kept our users informed. 
1.  **Create a Method for Sharding your Users** During our traffic onslaught, an engineer from Facebook suggested the #1 thing we should do is "hash or shard our users". By sharding we mean the ability to show a different version of your app to a fraction of users. In our case, needed to bring a bigger database online to support load. We knew the database needed time to warm up its in memory cache. This could only be done with queries to the data. If we had allowed all of our users to hammer the database before the in memory cache was ready, it would have crashed the system. So, we sharded our users. We let 5% of users access the app while the other 95% saw our productive error page. The cache warmed a bit. We then let in 10%... 20%... 50%... 75%... and finally 100% of users. Turns out sharding is useful in other ways.  You can use it to launch and load test new features. Show new feature to x% of users and see if you get bug reports or unexpected load spikes. When things look good with your sample users, then roll the feature out to everyone.
1.  **Know Your Outs** For every component in the system, know and test the next size up. In some cases this might be testing that migrations to the next bigger instance size actually work. In others cases, it might be testing that adding nodes to horizontal worker or web farms works well. Test this frequently. Always make sure you know your next step for each component of your app. Easy to do. Dev environment might be small. Production a bit bigger with more nodes. Moving between them should be push button no crazy deployment hoops to go to larger system.

In sum: with a productive error page, ability to shard users, and by knowing your outs, you should be ready to scale your app to the next order of magnitude. Doing much beyond that might be premature optimization. Doing less might mean a huge lost opportunity when the hungry crowd comes knocking.
