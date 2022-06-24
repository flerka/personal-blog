---
layout: post
title: Work with Time Zones on different OS with .Net Core
subtitle: 
tags: [development]
comments: true
---

Let's say you need to work with time zones in your .Net Core app and convert the date from UTC to a specific time zone or vice versa if you have a Windows development environment and a Linux production environment. What can go wrong?

Let's think of how time zones work in .Net Core. First, it relies on time zone information provided by the operating system. In the case of Windows, they have specific Microsoft naming and stored in the registry. On Linux .Net Core uses TZ database information that uses the Internet Assigned Numbers Authority (IANA) format.

So if you want to find time zones in different operating systems, you can get different results. How to solve this problem? Luckily, there is a library that solves this [TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter). So now instead of calling ```TimeZoneInfo.FindTimeZoneById()``` you use ```TZConvert.GetTimeZoneInfo()``` which will work correctly regardless of what OS you are running on. 

Some important notes: Alpine Linux Docker images for .NET Core don't have a [package with the TZ database](https://github.com/dotnet/dotnet-docker/issues/1366) installed, so you need to install tzdata manually.

__*You can subscribe to my [Telegram channel](https://t.me/dotnetarticles), where I post interesting .NET-related stuff and tech news.*__