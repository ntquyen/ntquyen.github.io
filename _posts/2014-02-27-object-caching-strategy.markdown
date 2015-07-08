---
author: quyennt
comments: true
date: 2014-02-27 03:40:50+00:00
excerpt: In this post I will explain more about object caching and how it helps avoiding
  most of requests to database.
layout: post
slug: object-caching-strategy
title: Introduction to Object Caching Strategy
wordpress_id: 275
categories:
- Web Development
tags:
- caching
- memcached
- redis
- strategy
---

In this post I will explain more about object caching and how it helps avoiding most of requests to database.

In the [previous post,](http://quyennt.com/web-development/server-side-caching-strategies/) I introduced the idea of object caching as in the figure below:

[![object caching1](http://quyennt.com/wp-content/uploads/2014/02/object-caching1.png)](http://quyennt.com/wp-content/uploads/2014/02/object-caching1.png)

[<!-- more -->](http://quyennt.com/wp-content/uploads/2014/02/object-caching1.png)

As illustrated in the figure, we will have two data structures:



	
  1. lists of object ids that are sorted by one criteria as your choice. Every object will be scored after that criteria (which might be date, price, number of likes....).

	
  2. object details which will be mapped from the lists basing on object ids.


For example, suppose I wanna apply object caching to my blog. So, I would consider all my blog posts as objects. And there are two more points to be figured out:

	
  * The posts will be stored in categories. So for each category, I will have a list of object ids belong to that category.

	
  * The object ids in each list will be sorted decreasingly by their published time.


Okay, that's the idea of storing data in cache. Now for the Q&A part:

**What type of caching system should I use?**

Well, I would recommend [Redis](http://redis.io) which is the most convenient. Because as far as I know, Redis is the only caching server that supports data structure in its value such as hashes, sets, lists, or sorted set. The sorted set is perfectly useful as it does the sorting for us and also supports pagination.

Besides, you can always choose your favorite caching system such as Memcached, or APC cache. However, those are just simple key-value caching, implementation will be more complicated as you have to implement yourself the sorted lists and pagination.

I did successfully implement this algorithm using Memcached with simple binary sort for lists. It was much more complicated than using Redis! Especially when you take into account the scale problem (when caching big dataset).

**What is the flow of client requesting after apply object caching?**

It is pretty simple. When a specific list is requested:


check in cache for the list




if the list is not yet stored in cache




get it from database




store it in cache (you might wanna use job queue for this operation to save response time)




endif




return the list


**Should I cache permanently or set a specific expiration time?**

As the objects may be updated constantly, so you should store them permanently and update the corresponded cache whenever there's a change. As I did below:

When an object's detail is changed:


Update the object detail cache accordingly.




Update its position in relevant lists if the sort criteria is changed.


**Conclusion**

Well, I think this it is enough to provide the basic idea and how to implement object caching algorithm. Most of all, you can see the power of this strategy that it helps to avoid most of requests to your database. Once data is stored in the cache, it is always there, there will be no need to check the database for later requests. Once all data is in the cache, there should be no request to database. So even when database is down, the site is still available!
