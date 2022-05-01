---
layout: post
title:  "Race condition"
published: true
author: nemanjan00
show_sidebar: true
---

Race condition is very common problem which is not mentioned so commonly and that is not so good. 

Problem lays in pretty simple fact. Code is not executed in real-time. 

Let's say we have web store. When customer buys something, our code first checks if customer have enough money. After that, if customer have enough	money, code checks if that item is currently available in store. If it is, then it takes amount it got while checking amount first time, subtract it with item price and update users credit. 

```
[QUERY USERS CREDIT]
[CHECK IF CREDIT > COST]
[CHECK IF ITEM IS AVAILABLE]
[SUBTRACT USERS CREDIT FROM STEP 1]
[UPDATE USERS CREDIT]
```

At first, it all looks cool... But, wait a minute! What if user send two requests one after another? 

```
[QUERY USERS CREDIT]
			[QUERY USERS CREDIT]
			[CHECK IF CREDIT > COST]
			[CHECK IF ITEM IS AVAILABLE]
			[SUBTRACT USERS CREDIT FROM STEP 1]
			[UPDATE USERS CREDIT]
[CHECK IF CREDIT > COST]
[CHECK IF ITEM IS AVAILABLE]
[SUBTRACT USERS CREDIT FROM STEP 1]
[UPDATE USERS CREDIT]
```

So, user just got two items for price of one. Not what we wanted, right? 

## Real-life demonstration

Just to prove how big of a problem this is, I created very simple demo. 

This is file which is vulnerable: 

```php
<?php
// Lets just read number from file
$counter = file_get_contents("./counter.txt");

//Increment it
$counter = $counter + 1;

//Ok, now, let's do something slow, not like something in counter.txt can change while we are doing this. Or can it? 
$n = Array(); 

for($i = 0; $i < 1000; $i++){
	$n[] = rand(1, 1000);
}

//Write new number
file_put_contents("./counter.txt", $counter);
```

This is file we are executing to exploit vulnerability: 

```php
<?php
for($i = 0; $i < 100; $i++){
	//execute it and do not wait :)
	system("php test.php  > /dev/null 2>/dev/null &");
}
```

So, this is how it looks like. Instead of 100, it outputs 58.

```bash
╭─nemanjan00@nemanjan00-laptop  ~/race-condition ‹› ‹master› 
╰─$ cat counter.txt 
0
╭─nemanjan00@nemanjan00-laptop  ~/race-condition ‹› ‹master› 
╰─$ php index.php 
╭─nemanjan00@nemanjan00-laptop  ~/race-condition ‹› ‹master*› 
╰─$ cat counter.txt
58 
```

## So, what is the solution?

If you are working with file, there is very simple solution. It is called [flock](http://php.net/manual/en/function.flock.php). Basically, you lock file while you work with it and scripts works with it one by one. 

```php
<?php
$filename = "./counter.txt";

//Let's open file
$fp = fopen($filename, "r+");

//Let's lock file
while(!flock($fp, LOCK_EX));

//Let's read file content
$counter = fread($fp, filesize($filename));

//Increment it
$counter = $counter + 1;

echo $counter;

//Ok, now, let's do something slow, not like something in counter.txt can change while we are doing this. Or can it? 
$n = Array(); 

for($i = 0; $i < 1000; $i++){
	$n[] = rand(1, 1000);
}

//Write new number
ftruncate($fp, 0);
fseek($fp, 0);
fwrite($fp, $counter);

fflush($fp);
flock($fp, LOCK_UN);

fclose($fp);
```

Here is example executed: 

```
╭─nemanjan00@nemanjan00-laptop  ~/race-condition ‹› ‹master*› 
╰─$ cat counter.txt     
0
╭─nemanjan00@nemanjan00-laptop  ~/race-condition ‹› ‹master*› 
╰─$ php index_secure.php
╭─nemanjan00@nemanjan00-laptop  ~/race-condition ‹› ‹master*› 
╰─$ cat counter.txt     
100
```

For database it is pretty similar process so I will not be describing it. If you need to find how to do it in other case, just google it. 
