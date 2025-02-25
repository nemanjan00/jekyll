---
layout: post
title:  "How to print all rejections in NodeJS"
published: true
author: nemanjan00
image: /assets/blur-bright-business-codes-207580.jpg
hero_image: /assets/blur-bright-business-codes-207580.jpg
hero_darken: true
show_sidebar: true
---

Did you forget to print error on rejection, in a huge project and are now having
issues debugging it? I wrote a snippet of code to solve that.

```javascript
const promiseProto = new global.Promise(() => {}).__proto__;

const originalCatch = promiseProto.catch; // grab original catch implementation

promiseProto.catch = function(...args) { // wrap around catch implementation, to print error
	const originalCallback = args[0];

	args[0] = (...errArgs) => {
		console.error(errArgs[0]);

		originalCallback(...errArgs);
	};

	return originalCatch.apply(this, args);
};

Promise.reject(new Error("Just a terrible error")).catch(() => {
	// forgoten rejection
});
```
