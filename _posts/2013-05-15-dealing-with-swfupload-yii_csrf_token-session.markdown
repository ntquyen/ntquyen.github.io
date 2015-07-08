---
author: quyennt
comments: true
date: 2013-05-15 19:17:37+00:00
excerpt: I've spent 2 days integrating swfupload into our CMS project, and I’ve earned
  an experience about flash uploading that I wanna talk about in this post.
layout: post
slug: dealing-with-swfupload-yii_csrf_token-session
title: Dealing with swfupload & YII_CSRF_TOKEN & SESSION
wordpress_id: 24
categories:
- Web Development
tags:
- flash uploader
- open source
- session
- Yii
- YII_CSRF_TOKEN
---

I've spent 2 days integrating [swfupload](https://code.google.com/p/swfupload/) into our CMS project, and I’ve earned an experience about flash uploading that I wanna talk about in this post.

Our [CMS](https://github.com/nganhtuan63/GXC-CMS) is written on [Yii Framework](http://www.yiiframework.com/), it uses YII_CSRF_TOKEN and [Authentication](http://www.yiiframework.com/doc/guide/1.1/en/topics.auth). That’s why integrating swfupload is not so easy..

<!-- more -->

The first problem is that **the flash uploader does not send cookie for security reason. So it cannot get through the CSRF validation.**

[caption id="attachment_25" align="alignnone" width="385"][![Browser-request](http://quyennt.com/wp-content/uploads/2013/05/Screen-Shot-2013-05-15-at-4.46.17-PM.png)](http://quyennt.com/wp-content/uploads/2013/05/Screen-Shot-2013-05-15-at-4.46.17-PM.png) Browser request[/caption]

[caption id="attachment_26" align="alignnone" width="390"][![Screen Shot 2013-05-15 at 4.47.07 PM](http://quyennt.com/wp-content/uploads/2013/05/Screen-Shot-2013-05-15-at-4.47.07-PM.png)](http://quyennt.com/wp-content/uploads/2013/05/Screen-Shot-2013-05-15-at-4.47.07-PM.png) Request by object tag[/caption]

We have one possible solution for this: Use `noCsrfValidationRoutes` attribute for the request url so that the HttpRequest will detach the url from the CSRF validation. The code would look like this:

    
    ...
    'request' => array(
    'class'=> 'cms.components.HttpRequest',
    'enableCsrfValidation' => true,
    'enableCookieValidation' => true,
    'noCsrfValidationRoutes'=> array('uri/to/upload/function')
    ),
    ...


Obviously, the solution above is not quite secure because the request url must be detached from CSRF validation. Even if we use that, we fall into the second problem: **The flash uploader creates its own session to do the request rather than user session.** And as the result, the request could pass the CSRF validation, but not the authentication!

So the solution is:



	
  * First, we use the CSRF validation with session instead of cookie. To do that, you just follow this [instruction](http://www.yiiframework.com/wiki/274/how-to-validate-csrf-token-with-session/).

	
  * Then, we pass the user session and the YII_CSRF_TOKEN to the request so that at sever side, we could retrieve it and restore the user session to the `session_id()` before calling `session_start()`.


The code to pass user session and the YII_CSRF_TOKEN would look like below:

    
    ...post_params: { "YII_CSRF_TOKEN" : "add your token here", "PHPSESSID":"add your user session here"},...


If you don’t know about swfupload installation, you should follow its [documentation](http://demo.swfupload.org/Documentation/). And the code to retrieve and restore the user session at server side would look like below:

    
    if(isset($_REQUEST['PHPSESSID'])) {
        session_id($_REQUEST['PHPSESSID']);
    }


That's it! The solution is simple, but I learned a good lesson about how flash works: **It doesn't include cookie and it creates its own session for its requests to server**.
