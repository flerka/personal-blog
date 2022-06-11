---
layout: post
title: Looking Back - What Happened In C# 8 Part 2
subtitle: 
tags: [development, csharp, dotnet,csharp8-series]
comments: true
---

This post is the second part of the series about C# 8.

All articles in the [series](https://flerka.github.io/personal-blog/tags/#csharp8-series)
- [part 1](https://flerka.github.io/personal-blog/2022-05-25-whats-new-in-net-csharp-8-part1/)
- [part 2](https://flerka.github.io/personal-blog/2022-05-28-whats-new-in-net-csharp-8-part2/)
- [part 3](https://flerka.github.io/personal-blog/2022-05-28-whats-new-in-net-csharp-8-part3/)

## Null-coalescing assignment
In C# 8, a new assignment operator was added - `??=`. So in the example below default user will be assigned only if the user variable is `null`.

```csharp
user ??= defaultUser;
````

Null-coalescing assignment operator is similar to `??`, which returns the value of its left operand if it is not null; otherwise, it evaluates the right operand and returns its result.

## Asynchronous streams

Microsoft team added a new interface - `IAsyncEnumerable<T>`. It provides an enumerator for asynchronous iteration similar to synchronous `IEnumerable<T>`. Why does it matter? Before this feature was implemented, you could not use `yield` in the async method. 

For example, with C# 8 you can write such code:

```csharp
        private async IAsyncEnumerable<string> GetFilesTextsAsync(string dirName) 
        {
            var files = Directory.GetFiles(dirName);
            foreach (var file in files)
            {
                if (File.Exists(file))
                {
                    yield return await File.ReadAllTextAsync(file);
                }
            }
        }
```

To consume sequence from `GetFilesTextsAsync` you can use the method presented below. As you see, this method is marked as `async` and has `await` before the `foreach` keyword.

```csharp
        private async static Task ProcessFilesInDirectory(string dirName)
        {
            await foreach(var fileText in GetFilesTextsAsync(dirName))
            {
                // text processing
            }
        }
```

## Asynchronous disposable
In C# 8 was added new interface, `IAsyncDisposable` for asynchronous disposal of unmanaged resources. To implement this interface, you should add to your code method `DisposeAsync`. C# also now supports `await using` statement.

## Other updates in C# 8
- constructed value type is unmanaged if it contains fields of unmanaged types only.
- you can use `stackalloc` result if it's `Span<T>` or `ReadOnlySpan<T>` in nested expressions.
- added support for default interface methods. You can now add default implementation to the interface members. This feature will be handy for library authors.

## What is next
In the next and the last part three, I will revise two major C# 8 updates - indices/ranges and nullable reference types. So I decided to dedicate the whole article to these two topics.

## What to read
- https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8
- https://docs.microsoft.com/en-us/dotnet/api/system.iasyncdisposable
- https://docs.microsoft.com/en-us/dotnet/standard/garbage-collection/implementing-disposeasync