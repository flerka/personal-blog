---
layout: post
title: Looking Back - What Happened In C# 8 Part 3
subtitle: 
tags: [development, csharp, dotnet,csharp8-series]
comments: true
---

In the third part of the series about C# 8, I will look at nullable reference types and indexes/ranges features.

All articles in the series:
- [part 1](https://flerka.github.io/personal-blog/2022-05-25-whats-new-in-net-csharp-8-part1/)
- [part 2](https://flerka.github.io/personal-blog/2022-05-28-whats-new-in-net-csharp-8-part2/)
- [part 3](https://flerka.github.io/personal-blog/2022-05-28-whats-new-in-net-csharp-8-part3/)

## Nullable references types
Why is this important? Before C# 8 and .Net 3.0, all reference types (such as classes or records) were considered nullable by default by the compiler. However, in more recent languages such as Swift or Kotlin, all reference types are not-nullable by default. Null reference types are also called "billion-dollar mistake" by its inventor Tony Hoare according to [Wikipedia](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions).

What problem do nullable reference types solve? First, it forces you as a developer to think about a situation when an object is not presented, for example, in the API response.

To understand when your reference variable will be considered nullable and when not, you should check [this](https://docs.microsoft.com/en-us/dotnet/csharp/nullable-references#nullable-contexts) article. It explains how nullable context works.

To mark the reference variable as nullable, you need to add the `?` operator; otherwise, if you are in the nullable context, compile-time validation will work to ensure that your variable won't be null.

## Index type
In C# 8 two new types were introduced - Index struct and Range struct. They were intended to improve work with arrays in C#.

To work with index type new operator was added - `^`, index from end operator. Example of usage with Index variable declaration:

```csharp
        var chars = new[] { 'a', 'b', 'c', 'd' };
        
        Index index0 = 0;
        Console.WriteLine(chars[index0]); // a
            
        Index indexLast = ^1;
        Console.WriteLine(chars[indexLast]); // d

        Index indexOutOfRange = ^0;
        try
        {
            Console.WriteLine(chars[indexOutOfRange]);
        }
        catch(Exception e)
        {
            Console.WriteLine(e); // System.IndexOutOfRangeException: Index was outside the bounds of the array.
        }
```

Example of in place operator usage
```csharp
        var chars = new[] { 'a', 'b', 'c', 'd' };
        
        Console.WriteLine(chars[0]); // a
        Console.WriteLine(chars[^1]); // d

        try
        {
            Console.WriteLine(chars[^0]);
        }
        catch(Exception e)
        {
            Console.WriteLine(e); // System.IndexOutOfRangeException: Index was outside the bounds of the array.
        }
```

## Range type
Range type was introduced to simplify access to subarray and to work with data overall. You need to use the range operator - `..` to work with it. It specifies the start and the end of a range of indices of an array. The first value is an inclusive start of a range, and the second value is an exclusive end of a range. 

Range type can be used similar to the Index type - with variable declaration and in-place. 

```csharp
        var chars = new[] { 'a', 'b', 'c', 'd' };

        Range firstTwo = 0..2;
        Console.WriteLine(chars[0..2]); // ab

        Console.WriteLine(chars[0..4]); // abcd
        
        // ranges can be used with indexes
        Console.WriteLine(chars[0..^0]); // abcd

        Range outOfRange = 0..5;
        try
        {
            Console.WriteLine(chars[outOfRange]);
        }
        catch(Exception e)
        {
            Console.WriteLine(e); // System.ArgumentOutOfRangeException: Specified argument was out of the range of valid values.
        }
```

__*You can subscribe to my [Telegram channel](https://t.me/dotnetarticles), where I post interesting .NET-related stuff and tech news.*__

## What to read
https://docs.microsoft.com/en-us/dotnet/csharp/nullable-references
https://docs.microsoft.com/en-us/dotnet/csharp/nullable-migration-strategies
https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#index-from-end-operator-