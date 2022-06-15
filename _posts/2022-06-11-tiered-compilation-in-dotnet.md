---
layout: post
title: Tiered Compilation in .Net Core
subtitle: 
tags: [development, dotnet]
comments: true
---

.Net Core team introduced tiered compilation in .Net 2.1 as a part of a bunch of [performance improvements](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/). At that time, it was turned off by default. Lately, in .Net 3.0, Tiered compilation was turned on by default, along with bug fixes and performance improvements related to this feature. Tiered compilation can be possible because of runtime [feature to re-jit](https://github.com/dotnet/coreclr/blob/master/Documentation/Profiling/davbr-blog-archive/ReJIT%20-%20The%20Basics.md) already jitted code. 

## How Tiered Compilation works
Tiered compilation improves program start time and runtime performance. It allows to compile CIL methods twice (the first compilation is called Tier 0, and the second is Tier 1). When the method is called for the first time, and JIT compiles it - this is Tier 0 compilation. Tier 0 does not include costly optimizations that can slow down method first call time and uses the Quick JIT feature. After some amount of calls, JIT will recompile the method - it's called Tier 1 compilation. Tier 1 compilations runs in the background and produces more optimized and performance-oriented code. CodeVersionManager will then replace Tier 0 with Tier 1 code in the runtime. 

## ReadyToRun and Tiered Compilation
Tiered Compilation works well with the ReadyToRun feature. Tiered compilation allows to re-jit CIL code in ahead-of-time compiled assemblies. Ahead-of-time produced machine code tended to be less performant in the long run because of its startup performance focus. .Net Core code is usually shipped in ReadyToRun state (precompiled), so not all processor-specific optimizations are turned on. Tiered Compilation improves the framework's performance on the end-user or server machines.

## Quick JIT and Tiered Compilation
Quick JIT helps with JIT compilation time but produces not very optimized code. It was introduced in .Net Core 2.1 and turned on by default in .Net Core 3.0. You can turn off Quit JIT, but Tiered Compilation will only work for [ReadyToRun](https://docs.microsoft.com/en-us/dotnet/core/deploying/ready-to-run) code. 

## Conclusion
Before the .Net team introduced Tiered Compilation, you had only two options - better runtime performance but slow start or the opposite - slower runtime performance and quick start with ahead-of-time compilation. Some applications even had a select box when installing software where it was asking you about your start time/runtime performance preferences. Tiered compilation allows having both - a quick start and good performance in the long run. From my perspective, long-living apps will significantly benefit from that.

## More details
If you really want to go to internals you can check:
- [code versioning](https://github.com/dotnet/runtime/blob/main/docs/design/features/code-versioning.md) feature overview and [code](https://github.com/dotnet/runtime/blob/main/src/coreclr/vm/codeversion.cpp)
- [tiered compilation](https://github.com/dotnet/runtime/blob/main/docs/design/features/tiered-compilation.md) feature overview and [code](https://github.com/dotnet/runtime/blob/main/src/coreclr/vm/tieredcompilation.cpp)

## What to read
- https://devblogs.microsoft.com/dotnet/tiered-compilation-preview-in-net-core-2-1/
- https://docs.microsoft.com/en-us/dotnet/standard/managed-execution-process
- https://mattwarren.org/2017/12/15/How-does-.NET-JIT-a-method-and-Tiered-Compilation/