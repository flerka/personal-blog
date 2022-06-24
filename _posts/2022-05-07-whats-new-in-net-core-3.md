---
layout: post
title: Looking Back - What Happened In .Net Core 3.0
subtitle: 
tags: [development, csharp, dotnet]
comments: true
---

I decided to write article series about .Net Core 3.0 and further versions. It will help me revise changes that happened in the last several years with .Net Core and may be helpful for someone else.

### Support for Windows desktop applications

 .Net Core 3 have added support for WPF and WinForms application. Also, .NET Core Windows Forms applications now support high DPI mode.

### New build-in JSON serializer

.Net team added a new namespace System.Text.Json. Build-in serializer from Microsoft could be used instead of Newtonsoft.Json.

### Support for C# 8.0
I wrote the whole series about C# 8. All articles in the [series](https://flerka.github.io/personal-blog/tags/#csharp8-series)
- [part 1](https://flerka.github.io/personal-blog/2022-05-25-whats-new-in-net-csharp-8-part1/)
- [part 2](https://flerka.github.io/personal-blog/2022-05-28-whats-new-in-net-csharp-8-part2/)
- [part 3](https://flerka.github.io/personal-blog/2022-05-28-whats-new-in-net-csharp-8-part3/)

### PublishReadyToRun

You can add PublishReadyToRun setting to your project. ReadyToRun technology now improves your app startup time by compiling your assemblies beforehand.

### .NET Core now builds framework-dependent executables by default

Previously .Net Core built framework-dependent dll files by default.

### Single file packaging

You can select the dotnet publish option that will create a single file executable OS specific that will include .Net Core runtime in it.

### Assembly trimming

.Net Core 3.0 SDK contains an assembly trimming tool. It helps you to reduce your app size.

### Local tools

.Net team added local-tools. However, they work only on the folder level and rely on dotnet-tools.json for configuration.

## Other updates in .Net Core 3

- [tiered compilation](https://flerka.github.io/personal-blog/2022-06-11-tiered-compilation-in-dotnet/) is now on by default
- macOS executable setting is turned off by default right now
- dotnet build now copies dependencies from NuGet cache to the output folder (before, only dotnet publish did that)
- .Net Core provides more accurate information about the environment (such as Runtime\Framework versions)
- added support of Serial Ports on Linux
- added Gpio support on Raspberry Pi  
- added support of ARM64 on Linux
- System.Net.Http.HttpClient now supports Http/2

__*You can subscribe to my [Telegram channel](https://t.me/dotnetarticles), where I post interesting .NET-related stuff and tech news.*__

## What to read
- https://docs.microsoft.com/en-us/dotnet/core/whats-new/dotnet-core-3-0
- https://andrewlock.net/series/exploring-asp-net-core-3/ Andrew Lock wrote a fantastic (as always) series that deep dives into .Net Core 3.0. You definitely should look into it.