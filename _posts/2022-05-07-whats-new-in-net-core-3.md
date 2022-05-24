---
layout: post
title: Looking Back - What Happened In .Net Core 3.0
subtitle: 
tags: [development],[C#],[dotnet]
comments: true
---

I decided to write article series about .Net Core 3.0 and further versions. This will help me to revise changes that happened in the last several years with .Net Core and maybe will be useful for someone else.

### Support for Windows desktop applications

In .Net Core 3 was added support for WPF and WinForms application. Also .NET Core Windows Forms applications now support high DPI mode.

### New build-in JSON serializer

Added new namespace System.Text.Json. Build-in serializer from Microsoft could be used instead of Newtonsoft.Json.

### Support for C# 8.0

### PublishReadyToRun

You can add PublishReadyToRun setting to your project. ReadyToRun technology now improves your app startup time by compiling your assemblies beforehand.

### .NET Core now builds framework-dependent executables by default

Previously .Net Core builded framework-dependent dll file by default.

### Single file packaging

You can select dotnet publish option that will create single file executable OS specific that will include .Net Core runtime in it.

### Assembly trimming

.Net Core 3.0 SDK contains assembly trimming tool. It helps you to reduce your app size.

### Local tools

local-tools added. They work only on folder level and rely on dotnet-tools.json for configuration.

## Other updates in .Net Core 3

- tiered compilation is now on by default
- macOS executable setting is turned off by default right now
- dotnet build now copy dependencies from NuGet cache to the output folder (before only dotnet publish did that)
- .Net Core provides more accurate information about environment (such as Runtime\Framework versions)
- added support of Serial Ports on Linux
- added Gpio support on Raspberry Pi  
- added support of ARM64 on Linux
- System.Net.Http.HttpClient now supports Http/2

## What to read

These article based on links listed below. To understand topic better you should definitely read them too:

- https://docs.microsoft.com/en-us/dotnet/core/whats-new/dotnet-core-3-0
- https://andrewlock.net/series/exploring-asp-net-core-3/ Andrew Lock wrote awesome (as always) series that deep dive into .Net Core 3.0. You definitely should look into it.
