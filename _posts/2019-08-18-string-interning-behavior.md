---
layout: post
title: String interning in C# with different code examples
subtitle: 
tags: [development]
comments: true
---

According to Eric Lippert [article](https://blogs.msdn.microsoft.com/ericlippert/2009/09/28/string-interning-and-string-empty/) on MSDN "If you have two identical string literals in one compilation unit, then the code we generate ensures that only one string object is created by the CLR for all instances of that literal within the assembly. This optimization is called string interning." So basically, if you have two identical string literals in your code, only one string will be created.

Here are some examples of how string interning works in different cases:

## 1 The simplest case is when both variables are string literals

```csharp
var hello = "hello";
var hello2 = "hello";
Console.WriteLine(hello == hello2);
Console.WriteLine((object)hello == (object)hello2);
```

In this case, string interning works as expected. Result:

```csharp
True
True
```

## 2 When one of the variables is the concatenation of two strings literals

```csharp
var hello = "hello";
var hello2 = "h" + "ello";
Console.WriteLine(hello == hello2);
Console.WriteLine((object)hello == (object)hello2); 
```

In this case, string interning also works as expected. Result:

```csharp
True
True
```

It works in such a way because of compiler optimization. We will get the same IL code as in the first example.

## 3 When one of the variables is a concatenation of string literal and const

```csharp
var hello = "hello";
const string h = "h";
var hello2 = h + "ello";
Console.WriteLine(hello == hello2);
Console.WriteLine((object)hello == (object)hello2); 
```

In this case, string interning also works as expected. Result:

```csharp
True
True
```

It works in such a way because of compiler optimization. We will get the same IL code as in the first example.

## 4 More complicated case with non-const variable

```csharp
var hello = "hello";
var h = "h";
var hello2 = h + "ello";
Console.WriteLine(hello == hello2);
Console.WriteLine((object)hello == (object)hello2);  
```

The result shows that the references are not the same:

```csharp
True
False
```

hello2 wasn't interned because it results from variable concatenation, not string literal concatenation.

## 5 Example with string interpolation

```csharp
var hello = "hello";
var h = "h";
var hello2 = $"{h}ello";
Console.WriteLine(hello == hello2);
Console.WriteLine((object)hello == (object)hello2);
```

The result shows that the references are not the same:

```csharp
True
False
```

Why does this happen? Again it's because string interning is compile-time optimization, not runtime.

## 6 Example with method Intern

```csharp
var hello = "hello";
var h = "h";
var hello2 = $"{h}ello";
var hello3 = String.Intern(hello2);
Console.WriteLine(hello == hello2);
Console.WriteLine(hello == hello3);
Console.WriteLine((object)hello == (object)hello2);
Console.WriteLine((object)hello == (object)hello3);
```

Result:
```csharp
True
True
False
True
```

What happens in this case? Method Intern is searching for a string in string pool and adding the new one if it does not find and returns it with reference. In our case string "hello" was already in string pool because of string literal _hello_ on line 1. When can it be useful? When you want to optimize memory usage in your application.

## What to read

I strongly recommend reading Eric Lippert's article on MSDN to understand the topic better. It is almost ten years old now, but it's still worth reading as it helps deeply understand the pros and cons of string interning in compile-time and runtime.

- https://blogs.msdn.microsoft.com/ericlippert/2009/09/28/string-interning-and-string-empty/
- https://docs.microsoft.com/en-us/dotnet/api/system.string.intern?view=netframework-4.8