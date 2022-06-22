---
layout: post
title: Ahead-of-Time Compilation in .NET
subtitle: 
tags: [development, dotnet]
comments: true
---
This post was inspired by the latest news about [Native AOT being the part of .NET 7](https://devblogs.microsoft.com/dotnet/announcing-dotnet-7-preview-3/#faster-lighter-apps-with-native-aot). So I decided to summarize past and current state of ahead-of-time compilation in dotnet ecosystem.

When we speak about ahead-of-time (AOT) compilation, we usually mean tools that allow us to generate native code in build-time instead of runtime ([JIT compilation](https://en.wikipedia.org/wiki/Just-in-time_compilation)). AOT binaries will be larger because they contain not only native code but an IL code too.

## AOT versus JIT compilation
JIT executes on the end-user/server machine, and AOT usually (ngen.exe is an exception, for example) runs on the developer machine after Roslyn compilation.

![Difference between AOT and JIT compilation.](../assets/aot-vs-jit.png)

## When you can benefit from AOT
- it helps to improve startup performance.
- it can [produces libraries](https://github.com/dotnet/samples/blob/main/core/nativeaot/NativeLibrary/README.md) that can be consumed by programs written in programming languages that don't use CLR.
- it can be used on the platform where JIT, for some reason, is not allowed. For example,[Xamarin.iOS uses ahead-of-time compilation](https://docs.microsoft.com/en-us/xamarin/ios/internals/architecture#aot) because of security restrictions iOS has. Apple doesn't allow the execution of dynamically generated code on iOS.

## Ahead-of-Time tools and approaches
- [ReadyToRun (R2R)](https://docs.microsoft.com/en-us/dotnet/core/deploying/ready-to-run) - current mainstream approach for AOT compilation in dotnet. It describes assembly format that contains IL + native code. In .NET 6.0 its performance was improved with Composite R2R Compilation. This feature allows cross-assembly optimization, such as the inlining method from another assembly. ReadyToRun supports cross-compilation.
- [mono AOT](https://www.mono-project.com/docs/advanced/aot/) - supports several modes: full and partial AOT. Full AOT for systems where JIT can't be run.
- [crossgen tool](https://github.com/dotnet/runtime/blob/main/docs/workflow/building/coreclr/crossgen.md) - later it was replaced with crossgen2.
- [crossgen2 tool](https://devblogs.microsoft.com/dotnet/announcing-net-6-preview-1/#crossgen2) - can generate assemblies in ReadyToRun format. It is the second version of crossgen tool, that the .NET team rewrote in C#. Crossgen2 has added features, such as [version bubbles](https://github.com/dotnet/runtime/blob/main/docs/design/features/crossgen2-compilation-structure-enhancements.md). The crossgen2 architecture uses a graph that allows performing different analysis and optimizations. There is great [article](https://devblogs.microsoft.com/dotnet/conversation-about-crossgen2/) that discusses crossgen2.
- [ngen.exe](https://docs.microsoft.com/en-us/dotnet/framework/tools/ngen-exe-native-image-generator) - for .Net framework. It runs on the target machine and produces executables that are not self-contained and requires an installed runtime. Can cause some [performance issues](https://devblogs.microsoft.com/dotnet/wondering-why-mscorsvw-exe-has-high-cpu-usage-you-can-speed-it-up/) on target PC.
- [CoreRT](https://github.com/dotnet/corert) - .NET Core runtime optimized for ahead-of-time compilation. Right now, this project was replaced by NativeAOT. It could use not only JIT compiler but experimental projects, like IL to CPP. It generated one file containing the app itself, its dependencies, and CoreRT runtime. If you want to know more, you can read a great [post](https://mattwarren.org/2018/06/07/CoreRT-.NET-Runtime-for-AOT/) from Matt Warren that has everything nicely written and illustrated. 
- [.Net Native](https://docs.microsoft.com/en-us/windows/uwp/dotnet-native/) - as far as I know, it's for UWP only.
- [IL2CPP](https://docs.unity3d.com/Manual/IL2CPP.html) - it compiles IL assemblies to native code, not open source, and for Unity only.
- [IL2CPU](https://github.com/CosmosOS/IL2CPU) - it's used in Cosmos project.

## Native AOT
Dotnet team presented Native AOT as a part of .NET 7 preview. Before that, it was part of the .NET Runtime Lab. 

How does Native AOT work, and what is the difference between __Native AOT__ and __ReadyToRun__? Right now, ReadyToRun assemblies have native code with native data structures + IL code. Such an approach - when you have both native, and IL code allows to execute Tiered Compilation and perform optimizations in runtime. Native AOT in opposite produces only native artifacts. It does not have IL code and cant be recompiled in any way with Tiered Compilation.

When Native AOT should be handy:
- in apps where size matters, Native AOT assemblies are smaller than ReadyToRun.
- short-lived scalable applications where Tiered Compilation won't kick in.

Cons of Native AOT:
- it has some [limitations](https://docs.microsoft.com/en-us/dotnet/core/deploying/native-aot#limitations-of-native-aot-deployment) related to its no-JIT nature and trimming mechanism to make this feature work.
- only Linux and Windows are supported for now.
- сross-OS compilation, such as targeting Linux on a Windows platform and visa versa, is not currently supported.
- it may not improve performance for long-living applications (because of the lack of Tiered Compilation with dynamic PGO).

In [microsoft development blog](https://devblogs.microsoft.com/dotnet/announcing-dotnet-7-preview-3/#faster-lighter-apps-with-native-aot), they gave a great example of Native AOT usage - crossgen2 tool. It's a short-lived tool that is now shipped in ReadyToRun format and can benefit from Native AOT. 

## What to read
- https://github.com/dotnet/runtime/blob/main/src/coreclr/nativeaot/docs/compiling.md
- https://docs.microsoft.com/en-us/dotnet/core/deploying/native-aot
- https://github.com/dotnet/coreclr/blob/master/Documentation/botr/readytorun-overview.md

I want to say thank you to [Artem Kolomytsev](https://github.com/SuddenGunter/) for review and proofreading of this post.