---
layout: post
title: Looking Back - What Happened In C# 8 Part 2
subtitle: 
tags: [development, csharp, dotnet]
comments: true
---

I decided to write article series about .Net Core 3.0 and further versions. This is the second part of the series about C# 8.

## Null-coalescing assignment
In C# 8 was added new assignment operator - `??=`. In the example below default user will be assigned only if user variable is `null`.

```csharp
user ??= defaultUser;
````

Null-coalescing assignment operator is similar to `??`, which returns the value of its left operand if it is not null; otherwise, evaluates the right operand and returns its result.

## Asynchronous streams

New generic interface was added - `IAsyncEnumerable<T>`. It provides an enumerator for asynchronous iteration similar to synchronous `IEnumerable<T>`. Why does it matter? Before this feature was implemented you could not use `yield` in async method. 

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

To consume sequence from `GetFilesTextsAsync` you can use method presented below. As you see this method is marked as `async` and has `await` before `foreach` keyword.

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
In C# 8 was added new interface `IAsyncDisposable` for asynchronous disposal of unmanaged resources. To implement this interface you should add to your code method `DisposeAsync`. C# also now supports `await using` statement.

## Other updates in C# 8
- now a constructed value type is unmanaged if it contains fields of unmanaged types only.
- you can use `stackalloc` result if it's `Span<T>` or `ReadOnlySpan<T>` in nested expressions.
- added support of default interface methods. You can now add default implementation to the interface members. This will be especially useful for library authors.

## What is next
In the next and the last part 3 I revise two major C# 8 updates - indices/ranges and nullable reference types. I decide to dedicate whole article to this two topics.

## What to read
- https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8
- https://docs.microsoft.com/en-us/dotnet/api/system.iasyncdisposable
- https://docs.microsoft.com/en-us/dotnet/standard/garbage-collection/implementing-disposeasync
