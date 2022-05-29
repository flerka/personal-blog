---
layout: post
title: Looking Back - What Happened In C# 8 Part 1
subtitle: 
tags: [development, csharp, dotnet]
comments: true
---

I decided to write article series about .Net Core 3.0 and further versions. This is the first part of the series about C# 8.

C# 8 was introduced in .Net Core 3 and it is supported in .NET Standard 2.1.

## Readonly structure members

Starting from the C# you can declare not only structures as readonly but structure members too. For example:

```csharp
public readonly double Mul()
{
    return X * Y;
}
```

In this case you cannot change structure members or you will see compile-time error. For example:

```csharp
        public readonly int Mul()
        {
            X *= Y;
            return X * Y;
        }
```

After compilation of code above you will see such error message:
***Error CS1604 Cannot assign to 'X' because it is read-only***

What you should remember about readonly structure members:

- you can use readonly with override keyword
- if you call non-readonly member  inside readonly one defensive copy will be created with compile warning:

***Warning CS8656 Call to non-readonly member from a 'readonly' member results in an implicit copy of 'this'.***

## Improved pattern matching

Switch syntax was simplified:

- `default` was replaced with discard symbol `_`
- `case` and `:` were replaced with `=>` statement
- variables now come before `switch`

Added support of property values and tuples types inside switch expression.

## Using declaration

Using statement can now be replaced with using declaration.

Example of using declaration:

```csharp
using var con = new SqlConnection(conString);
```

Example of using statement:

```csharp
using (var connection = new SqlConnection(conString))
{

}
```

**Important notion**, in case of using declaration object will be disposed when end of the scope where it was created will be reached. In case of using statement, object is disposed when the closing brace associated with the using statement is reached.

## Static local functions

Local functions was introduced in C# 7. From my point of view, static keyword for local functions is very useful. It allows you to avoid accidental closure. Closure in local function can lead to non-intuitive memory allocation. You can find more information about this in this article - https://weblogs.asp.net/dixin/functional-csharp-local-function-and-closure. I found this article very useful and really well-written.

## What to read
- https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/struct#readonly-instance-members
- https://weblogs.asp.net/dixin/functional-csharp-local-function-and-closure
- https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/local-functions
