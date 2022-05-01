---
layout: post
title:  "OAuth2 tricks"
published: true
author: nemanjan00
show_sidebar: true
---

My friend was quite confused about OAuth 2 today (facebook specifically) so I decided to write a couple of tricks for facebook and instagram authentication. 

## Types of authentication on Instagram/Facebook

There are two types of authentification. 

* With backend callback
* With frontend callback (facebook specific implementation)

### Backend callback

On your redirect url you are getting ``code`` GET argument and that code is what you use to get ``token``. 

### Frontend callback (facebook implementation)

When you trigger authentication from javascript, facebook sdk opens popup for authentication and when popup authentification is finished, window will get closed and you will get ``token`` back. 

## It is all OAuth 2, why is facebook different? 

Most modern applications today are modern and it is just not natural experience to have redirect and to reaload page. 

A lot of people would like to use same style of authentification for Instagram as for facebook. 

### What is the actual difference between facebook auth and normal OAuth2? 

What facebook did is build one more layer on top of OAuth. 

They developed backend that just returns token to your frontend application. 

## How do I do same thing for instagram? 

Before we implement the same thing for instagram, we need to get familiar with couple of concepts. 

### Opening window

```javascript
var window = window.open(url, windowName, [windowFeatures]);
```

When you open popup window, you are getting window object back and now, you can reference it. 

In window you opened you can reference parent using ``opener``

### Cross window communication

#### Sending data

```javascript
window.postMessage(message, targetOrigin, [transfer]);
```

In this case, you are sending ``message`` which is data object to ``window`` if target origin matches with origin of that window. 

Transfer is optional object that will be removed from your scope and added to that message. It can also contain stuff that is not data. 

#### Listening for data

```javascript
window.addEventListener("message", receiveMessage, false);

function receiveMessage(event)
{
  if (event.origin !== "http://example.org:8080")
    return;

  // ...
}
```

### Implementing instragram login

What we now need is backend script that will grab token from code and send it to window that called it. 

```javascript
opener.postMessage("put token here", "origin"); // To make sure noone steals token, use origin
```

In window that needs to login, we are opening instagram login in popup and put link to that backend script in redirect. 

```javascript
window.open("https://api.instagram.com/oauth/authorize/?client_id=APPLICATION_ID&redirect_uri="+window.location + "auth/instagram"+"&response_type=code");
```

Now, we need to wait for token response

```javascript
window.addEventListener("message", receiveMessage, false);

function receiveMessage(event){
	$window.removeEventListener("message", receiveMessage, false);
	// Token is in event.data
}
```

