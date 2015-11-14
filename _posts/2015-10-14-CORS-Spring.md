---
layout: post
title: Cross-origin resource sharing + Spring
comments: true
---

Cross-origin resource sharing (CORS) is a mechanism that allows restricted resources (e.g. fonts) on a web page to be requested from another domain outside the domain from which the resource originated
For example, as you’re checking your bank account in one tab, you could have the evil.com website in another tab. The scripts from evil.com shouldn’t be able to make AJAX requests to your bank API (withdrawing money from your account!) using your credentials.

if you are using spring 4.2+ just add these line in WebConfig file:

<script src="https://gist.github.com/a-majidi/2fa80473b854d2f8b59d.js"></script>

for other versions of spring add a controller like this:

<script src="https://gist.github.com/a-majidi/ea090fe9d0871017bd93.js"></script>


and lines added in web.xml:

<script src="https://gist.github.com/a-majidi/3f9eecb38ecfd99b5aa8.js"></script>




