---
layout: post
title: Ahead-of-Time Compilation in .NET
subtitle: 
tags: [development, dotnet]
comments: true
---
## Table of Contents
- [What is AOT](#what-is-aot)
  - [AOT vs JIT compilation](#aot-vs-jit-compilation)
  - [When you can benefit from AOT](#when-you-can-benefit-from-aot)
- [Ahead-of-Time tools and approaches](#ahead-of-time-tools-and-approaches)
- [Native AOT](#native-aot)
- [Let's try Native AOT](#lets-try-native-aot)
  - [Setup .NET 7 and create a sample project](#setup-net-7-and-create-a-sample-project)
  - [Sample applications measurements](#sample-applications-measurements)
- [What to read](#what-to-read)

This post was inspired by the latest news about [Native AOT being the part of .NET 7](https://devblogs.microsoft.com/dotnet/announcing-dotnet-7-preview-3/#faster-lighter-apps-with-native-aot). So I decided to summarize past and current state of ahead-of-time compilation in dotnet ecosystem.

## What is AOT
When we speak about ahead-of-time (AOT) compilation, we usually mean tools that allow us to generate native code in build-time instead of runtime ([JIT compilation](https://en.wikipedia.org/wiki/Just-in-time_compilation)). AOT binaries will be larger because they contain not only native code but an IL code too.

### AOT vs JIT compilation
JIT executes on the end-user/server machine, and AOT usually (ngen.exe is an exception, for example) runs on the developer machine after Roslyn compilation.

![Difference between AOT and JIT compilation.](../assets/aot-vs-jit.png)

### When you can benefit from AOT
- it helps to improve startup performance. 
- it can [produces libraries](https://github.com/dotnet/samples/blob/main/core/nativeaot/NativeLibrary/README.md) that can be consumed by programs written in programming languages that don't use CLR.
- it can be used on the platform where JIT, for some reason, is not allowed. For example, [Xamarin.iOS uses ahead-of-time compilation](https://docs.microsoft.com/en-us/xamarin/ios/internals/architecture#aot) because of security restrictions iOS has. Apple doesn't allow the execution of dynamically generated code on iOS.

## Ahead-of-Time tools and approaches
- [ReadyToRun (R2R)](https://docs.microsoft.com/en-us/dotnet/core/deploying/ready-to-run) - current mainstream approach for AOT compilation in dotnet. It describes an assembly format that contains IL + native code. In .NET 6.0 its performance was improved with Composite R2R Compilation. This feature allows cross-assembly optimization, such as the inlining method from another assembly. ReadyToRun supports cross-compilation.
- [mono AOT](https://www.mono-project.com/docs/advanced/aot/) - supports several modes: full and partial AOT. Full AOT for systems where JIT can't be run.
- [crossgen tool](https://github.com/dotnet/runtime/blob/main/docs/workflow/building/coreclr/crossgen.md) - later it was replaced with crossgen2.
- [crossgen2 tool](https://devblogs.microsoft.com/dotnet/announcing-net-6-preview-1/#crossgen2) - can generate assemblies in ReadyToRun format. It is the second version of the crossgen tool that the .NET team rewrote in C#. Crossgen2 has added features, such as [version bubbles](https://github.com/dotnet/runtime/blob/main/docs/design/features/crossgen2-compilation-structure-enhancements.md). The crossgen2 architecture uses a graph that allows performing different analysis and optimizations. There is great [article](https://devblogs.microsoft.com/dotnet/conversation-about-crossgen2/) that discusses crossgen2.
- [ngen.exe](https://docs.microsoft.com/en-us/dotnet/framework/tools/ngen-exe-native-image-generator) - for .Net framework. It runs on the target machine and produces executables that are not self-contained and requires an installed runtime. Can cause some [performance issues](https://devblogs.microsoft.com/dotnet/wondering-why-mscorsvw-exe-has-high-cpu-usage-you-can-speed-it-up/) on target PC.
- [CoreRT](https://github.com/dotnet/corert) - .NET Core runtime optimized for ahead-of-time compilation. Right now, this project has been replaced by Native AOT. It could use not only JIT compiler but experimental projects, like IL to CPP. It generated one file containing the app itself, its dependencies, and CoreRT runtime. If you want to know more, you can read a great [post](https://mattwarren.org/2018/06/07/CoreRT-.NET-Runtime-for-AOT/) from Matt Warren that has everything nicely written and illustrated. 
- [.Net Native](https://docs.microsoft.com/en-us/windows/uwp/dotnet-native/) - as far as I know, it's for UWP only.
- [IL2CPP](https://docs.unity3d.com/Manual/IL2CPP.html) - it compiles IL assemblies to native code, not open source, and for Unity only.
- [IL2CPU](https://github.com/CosmosOS/IL2CPU) - it's used in Cosmos project.

## Native AOT
The dotnet team presented Native AOT as a part of the .NET 7 preview. Before that, it was part of the .NET Runtime Lab. It's main goal to improve performance for applications, where JIT doesn't needed.

How does Native AOT work, and what is the difference between __Native AOT__ and __ReadyToRun__? Right now, ReadyToRun assemblies have native code with native data structures + IL code. Such an approach - when you have both native, and IL code allows to execute Tiered Compilation and perform optimizations in runtime. Native AOT in opposite produces only native artifacts. It does not have IL code and can't be recompiled in any way with Tiered Compilation.

When Native AOT should be handy:
- in apps where size matters, Native AOT assemblies are smaller than ReadyToRun.
- for short-lived cloud applications, such as Azure Functions, where you pay for execution time.
- short-lived scalable applications where Tiered Compilation won't kick in.

Cons of Native AOT:
- it has some [limitations](https://docs.microsoft.com/en-us/dotnet/core/deploying/native-aot#limitations-of-native-aot-deployment) related to its no-JIT nature and trimming mechanism to make this feature work.
- only Linux and Windows are supported for now.
- сross-OS compilation, such as targeting Linux on a Windows platform and visa versa, is not currently supported.
- it may not improve performance for long-living applications (because of the lack of Tiered Compilation with dynamic PGO).

In [microsoft development blog](https://devblogs.microsoft.com/dotnet/announcing-dotnet-7-preview-3/#faster-lighter-apps-with-native-aot), they gave a great example of Native AOT usage - crossgen2 tool. It's a short-lived tool that is now shipped in ReadyToRun format and can benefit from Native AOT. 

## Let's try Native AOT
Now let's create a sample application with Native AOT. I created additional projects to compare result file size and execution time. You can find them all in separate folders in my sample GitHub repository. I specified `SelfContained` for all of them except the Native AOT project. For Native AOT dotnet team used a similar mechanism to self-contained, but it's not the same. You can't specify `SelfContained` for the Native AOT project.
My sample projects compilation strategies: 
- Native AOT turned on
- ReadyToRun
- ReadyToRun with Quick JIT for loops
- project with default compilation settings - ReadyToRun is off, and QuickJIT is on
- project with ReadyToRun turned off, and QuickJIT turned off

### Setup .NET 7 and create a sample project
This instruction works for June 22, 2022:
- download and install [Visual Studio 2022 preview](https://docs.microsoft.com/en-us/visualstudio/releases/2022/release-notes-preview), including Desktop development with C++ component.
- download and install [.NET 7.0 Preview sdk](https://dotnet.microsoft.com/en-us/download/dotnet/7.0).
- create a sample application. Run in console `dotnet new console `.
- add PublishAot property to your project file `<PublishAot>true</PublishAot>`.

### Sample applications measurements
Because one of the most significant advantage of ahead-of-time compilation is quicker app start, I decided to measure the application run from start to end. I didn't find how to measure this with BenchmarkDotNet, so I decided to try bash tools. I tried `time` and `strace -c,` but both of them didn't give the expected precision. As a result, with the help of [SuddenGunter](https://github.com/SuddenGunter/) and [stackoverflow](https://stackoverflow.com/a/63635704), we created a small bash script that measures execution time. You can find it on [my Github](https://github.com/flerka/aot-samples/blob/main/measure-execution).

To measure the average time, I ran my [sample program](https://github.com/flerka/aot-samples) 1000 times on Windows x64 pc.
```
for run in {1..1000}; do (./measure-execution ./aot-samples.exe.exe >/dev/null) &>> result.txt; done
```
All applications were published with `dotnet publish -c Release`

Comparison of execution time. In my [samples](https://github.com/flerka/aot-samples), I used my naive solution from Advent of code and it executes pretty quick. 

| Compilation type | Exeсution avg in seconds| 
|-------|--------|
| Native AOT | 0.0512868349 |
| R2R | 0.07488370891 |
| R2R + Quick JIT for loops | 0.07483450931 |
| Default settings - R2R off + QuickJIT on | 0.07557441812 |
| Default + QuickJIT off | 0.074214229 |

Comparison of executable files. 

| Compilation type | Exe size in KB| 
|-------|--------|
| Native AOT | 4 622 |
| R2R | 64 580 |
| R2R + Quick JIT for loops | 64 580 |
| Default settings - R2R off + QuickJIT on | 64 567 |
| Default + QQuickJIT off | 64 567 |

## What to read
- https://github.com/dotnet/runtime/blob/main/src/coreclr/nativeaot/docs/compiling.md
- https://docs.microsoft.com/en-us/dotnet/core/deploying/native-aot
- https://github.com/dotnet/coreclr/blob/master/Documentation/botr/readytorun-overview.md


__*I want to thank [Artem Kolomytsev](https://github.com/SuddenGunter/) for the review and proofreading of this post.*__