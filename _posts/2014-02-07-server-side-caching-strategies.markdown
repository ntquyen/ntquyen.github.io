---
author: quyennt
comments: true
date: 2014-02-07 03:52:33+00:00
excerpt: Server-side caching has been a very attractive subject to me when I was developing
  caching strategy for my company's news website that currently serves ~ 2 million
  page views per day. Normally, we apply cache to reduce database requests, and our
  goal is the same. Our caching strategy is meant to the goal that even when database
  died, the site is still working.
layout: post
slug: server-side-caching-strategies
title: Server-side Caching Strategies
wordpress_id: 244
categories:
- Web Development
tags:
- caching
- object caching
- page caching
- strategy
---

Server-side caching has been a very attractive subject to me when I was developing caching strategy for my company's news website that currently serves ~ 2 million page views per day. Normally, we apply cache to reduce database requests, and our goal is the same. Our caching strategy is meant to the goal that even when database died, the site is still working.

That may sound crazy, but it's possible. But how?

<!-- more -->

First, let's talk about two ways of using cache at server-side:


## Page Caching


This strategy is very simple: for each request, we cache the whole response, specified by its URI. We can set the an expiration time or/and invalidate it whenever there is a change.


#### Pros:





	
  * Very simple and so very fast. If a URL is requested, return the whole page content found in the cache without further calculation.




#### Cons:





	
  * Cannot be use with session because of the difference of a page content between sessions.


	
  * Cannot be used to cache data retrieved by javascript/ajax.

	
  * If you invalidate cache at runtime, a tiny change would cause needless invalidation of the whole page

	
  * when the cache is invalidated, it must query the database. That could cause bottle-neck problem at that moment.




## Object caching


The idea of object caching is that every information on your page that you want to cache will be considered an object. Such as articles, products, or even product categories… can be objects. Those objects will be cached based on filtered information so that they can be well retrieved on the corresponding block of the page.


#### Pros:





	
  * Can be used freely, session or ajax problems do not matter anymore


	
  * When there’s change, we just need to update the cache data that corresponds to the changed object, rather than invalidating the whole page.

	
  * Avoid connections to database because once data is cached, it doesn't need to be invalidated.




#### Cons:





	
  * Slower than page caching.

	
  * Can be very complicated to implement, because the behavior for each type of object is different. You need to well plan and design before implementing.


I only know these two strategies that I used them both in our websites. But I believe that there's more, if you know, please tell me in the comments.
