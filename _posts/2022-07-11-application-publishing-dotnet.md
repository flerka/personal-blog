---
layout: post
title: Application Publishing Types in .NET
subtitle: 
tags: [development, dotnet]
comments: true
---
In this short post, I want to revise the application publishing types in dotnet. There are two types right now - self-contained and framework-dependent publishing. 

## Self-Contained Publishing
In the case of self-contained publishing, your application will be shipped with .NET runtime. A self-contained approach requires the developer to specify the target platform identifier. With this approach highest .NET patch version will be selected within the specified major.minor version - an analog of __x.x.*__ template. Self-contained application may be [trimmed](https://docs.microsoft.com/en-us/dotnet/core/deploying/trimming/trimming-options) to reduce IL size. 

Pros of this approach:
- your program becomes more portable
- end-user don't need to have a specific .NET version installed
- your code execution becomes more repeatable and reproducible especially compared to framework-dependent publishing and roll-forward feature turned on

Cons of this approach:
- you should know the target platform beforehand and publish separately for each platform 
- result application size is larger as it has .NET runtime in it
- you can't benefit from Docker [multi-stage build](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#use-multi-stage-builds)

## Framework-Dependent Publishing
In case of framework-dependent, your application will be shipped without .NET runtime. Therefore, to run your application, .NET runtime should be installed on the end-user/server machine. 

Pros of this approach:
- if the target file format is dll, it can be executed on any platform
- published application size is smaller
- if you use Docker, you can take advantage of its multi-stage build
- on the end machine user can manually update runtime to get a new security update

Cons of this approach:
- you should have the runtime installed on the target machine

### Framework-dependent publishing and roll-forward
.NET added a roll-forward feature that works with framework-dependent publishing. Roll forward is a somewhat smart version selection. This feature will affect what .NET version will be used for your application on the end-user machine. I strongly recommend checking [section about roll-forward](https://docs.microsoft.com/en-us/dotnet/core/versions/selection#framework-dependent-apps-roll-forward) as it is turned on by default. 

## How to make app self-contained
To publish self-contained Windows x65 application you need to do:
- this `dotnet publish -r win-x64 --self-contained true`
- or this `dotnet publish` and `<SelfContained>true</SelfContained>` and `<RuntimeIdentifier>win-x64</RuntimeIdentifier>` in project file
- or this `dotnet publish -r win-x64` and `<SelfContained>true</SelfContained>` in project file

## Important notes
- if you want your application to be executable, it always is platform-specific. If you run `dotnet publish` without parameters and don't specify the target platform in the project file, your current platform will be the target.
- to run framework-dependent dll on the target machine, run the command `dontet TARGETDLL_NAME` 
- to specify the result platform, you need to add `-r <RID>` to your publish command
- you can publish your self-contained application in [ready-to-run format](https://docs.microsoft.com/en-us/dotnet/core/deploying/#publish-with-readytorun-images)
- single-file deployment compatible with both self-contained and framework-dependent approaches
- single-file deployment has its nuances. I recommend checking [Microsoft documentation about single-file deployment](https://docs.microsoft.com/en-us/dotnet/core/deploying/single-file/overview)

__*You can subscribe to my [Telegram channel](https://t.me/dotnetarticles), where I post interesting .NET-related stuff and tech news.*__

## What to read
- [https://docs.microsoft.com/en-us/dotnet/core/deploying/](https://docs.microsoft.com/en-us/dotnet/core/deploying/)
- [https://docs.microsoft.com/en-us/dotnet/core/versions/selection#framework-dependent-apps-roll-forward](https://docs.microsoft.com/en-us/dotnet/core/versions/selection#framework-dependent-apps-roll-forward)
- [https://docs.microsoft.com/en-us/dotnet/core/deploying/single-file/overview](https://docs.microsoft.com/en-us/dotnet/core/deploying/single-file/overview?tabs=cli)