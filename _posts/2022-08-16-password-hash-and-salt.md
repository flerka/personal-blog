---
layout: post
title: Password Hashing and Salt
subtitle: 
tags: [development, cryptography]
comments: true
---
I created this short post to illustrate why you need unique password salt per user for [DevJungles](https://www.youtube.com/c/DevJungles) chat discussion (and got a cool DevJungles mug as a result!). In this post, I don't want to go into detail about [rainbow attack](https://en.wikipedia.org/wiki/Rainbow_table) or what is [salt in cryptography](https://en.wikipedia.org/wiki/Salt_(cryptography)). Let's start!

*<font color="green">Disclaimer: I used MD5 to generate hashes for simplicity, never use it in production for passwords because it's too weak. Nowadays, it would be best to use Argon2 or bcrypt for password hashing.</font>*

## Login form
For now, you have two different users with the same passwords. Unfortunately, their passwords are very common, and you can probably find them in the attacker table with passwords and hashes.
![Password salt login](../assets/psd-salt-login.png)

## Password hashing without salt
If you use the approach to store the hash of the password and don't use salt: 
![attacker space example 1](../assets/psd-salt-ex1.png).

The attacker will need **0 minutes** to get your password from the hash as they already have a rainbow table with the most popular passwords.

## Password hashing without unique salt
If you use the approach to store the hash of the password and use the same salt for all users: 
![attacker space example 1](../assets/psd-salt-ex2.png).

The attacker will need **N minutes** (N depends on the number of passwords in their rainbow table) to get your password from the hash as they can generate a rainbow table with your particular salt.

## Password hashing with unique salt
If you use the approach to store the hash of the password and you use the unique salt for each user: 
![attacker space example 1](../assets/psd-salt-ex3.png).

The attacker will need **N * K minutes** (where K is the number of users) to get your password from the hash, as they can generate a rainbow table for each salt.

__*You can subscribe to my [Telegram channel](https://t.me/dotnetarticles), where I post interesting .NET-related stuff and tech news.*__

## What to read
- [https://en.wikipedia.org/wiki/Rainbow_table](https://en.wikipedia.org/wiki/Rainbow_table)
- [https://en.wikipedia.org/wiki/Salt_(cryptography)](https://en.wikipedia.org/wiki/Salt_(cryptography))
- [https://security.stackexchange.com/questions/17421/how-to-store-salt](https://security.stackexchange.com/questions/17421/how-to-store-salt)