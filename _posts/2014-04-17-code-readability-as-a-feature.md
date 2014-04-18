---
layout: post
title: Read More. Code Readability as a Feature.
tags: [ code, design, github ]
---

Recently I read [Things You Should Never Do, Part I](http://www.joelonsoftware.com/articles/fog0000000069.html).

One line really stood out to me:

> It’s harder to read code than to write it.

In fact, I think it is **a lot** harder. I bet that it is at least 10x harder to read and understad code than it is to write it. 

If this is the case, we need to value readability of our code almost just as much as we value it working. Readability is what makes it possible to hand the code to the next developer and have them hit the ground running without having to fight the "let's just rewrite it urge" so much. 

Code readability might be the key feature to making our projects sustainable. 

Like a paragraph, code can be crafted to be more readable. (Here is the best book I have seen on crafting paragraphs [Style: Lessons in Clarity and Grace](http://www.amazon.com/Style-Lessons-Clarity-Grace-Edition/dp/0205747469) )

My teams are starting to use pull requests and code comments in Github to try to make our code more readable. Pull Requests give other developers on the team a great way to look at changes being made and comment if they do not understand. A side bennifit is that developers reading the code pick up on best practices that others are using and are familliar with others parts of our systems. 

Basic idea for the workflow is: before committing to master, create a pull request. Have someone else read it, comment, understand it and merge with master.

I will let you know if this improves readabiltiy. 