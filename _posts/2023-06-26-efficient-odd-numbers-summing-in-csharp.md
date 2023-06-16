---
title: "Efficient Odd Number Summing in C#/.NET"
date: 2023-06-16
categories:
  - Pogramming
tags:
  - C#
  - .NET
  - Performance
---

As developers, we often encounter situations where we need to perform specific operations on arrays efficiently. In this blog post, I will share my journey of creating a performance suite for C#/.NET focused on this particular problem and discuss the different approaches I explored.

I've choose the issue of summing up all the odd numbers in a given array as reference problem.

# TL;DR: The Winner is… `foreach`

I started by experimenting with various solutions, ranging from concise and idiomatic one-liners to low-level implementations inspired by languages like C and C++. Through this process, I aimed to strike a balance between readability and performance.

As expected, the the solution using the `foreach` loop provided the best compromise, delivering both idiomatic code and excellent performance.

The `foreach` loop in C# allows for elegant and readable code, while still benefiting from the underlying optimizations of the .NET runtime.

You can find the source code [here](https://github.com/MrBogomips/DotnetPerfLab/blob/main/Tricks/SummingOddNumbers.cs).

# The «Tricks»

Before exploring the most relevant solutions, a brief section about some tricks used to improve the performance.

## Trick #1: Parity Check Techniques

It's well known that when it comes to check for parity a more performant alternative to `odd % 2 == 1` (modulo operator) is the `odd & 1 == 1` (bit-and operator).

In the example reproduced in this post I will not spend details comparing the two alternative. Please reference the source code for details.

## Trick #2: Remove Branches

Branches, i.e. `if/else/switch` statements, will produce cpu instructions that will limitate the super-scalarity feature.
For this specific problem it's easily avoidable by observing that the parity check result can be used to nullify the element added to the sum in case of even numbers.

In practice, the block:

``` csharp
if (num % 2 == 1) sum += num;
```

is equivalend to:

``` csharp
sum += (num % 2) * num;
```

or event better, exploiting the trick #1:

``` csharp
sum += (num & 1) * num;
```

## Trick #3: Loop Unrolling

Another well known tecnique when implementing a strict loop is to step by 2 or more items at each loop. This trick benefits by CPU's superscalarity that essentially allow multiple istructions to be pushed into the pipeline in parallel.
The drawback is that particular attention must be deserved to manage tail conditions.

## Trick #4: Relaxing Runtime Checks (Unsafe)

Unsafe code reduces the number of runtime checks and will speed up the execution significantly.
The drawback is that your code resembles C/C++ rather than C#.

# The Journey

This is a list of the relevant solutions, from the worst one to the most effcient.

## Linq One-Liner: the functional one

> Score: 1 (The higher is the worst)

I love functional style and maybe this is the most elegant solution: clear and coincise:

``` csharp
var sum = array.Where(e => (e % 2) == 1).Sum();
```

Unfortunately this one is also the worst one!!!.

I will take the performance of this implementation as the baseline with a cost of 1.

## `foreach` loop: the idiomatic

> Score: 0.12

It's well knwon that the `foreach` is heavly optimized:

``` csharp
int sum = 0;
foreach (var e in array) sum += (e & 1) * e;
return sum;
```

This solution is clear, idiomatic and very efficient.
As C# claims, this should be the preferred way to manipulate arrays.

## `Span<T>`

> Score: 0.12

``` csharp
int sum = 0;
Span<int> s = array;
foreach (var e in s) sum += e * (e & 1);
```

## The fastest

> Score: 0.10

This version unrolls the loop by a factor 4 resembling the well known device used in Quake :).


``` csharp
int sum = 0;
int count = array.Length;
unsafe {
  fixed(int *pa = &array[0])
  {
    int* p = pa;
    for (int i = 0; i < count; i += 4)
    {
      int a0 = pa[0];
      int a1 = pa[1];
      int a2 = pa[2];
      int a3 = pa[3];
      sum += a0 * (a0 & 1);
      sum += a1 * (a1 & 1);
      sum += a2 * (a2 & 1);
      sum += a3 * (a3 & 1);
      p += 4;
    }
  }
}

return sum;
```

Neither to mention that this effort could not be so valuable provided the `foreach` alternative.

# Benchmark Data


``` ini

BenchmarkDotNet=v0.13.5, OS=macOS Ventura 13.4 (22F66) [Darwin 22.5.0]
Apple M1 Pro, 1 CPU, 10 logical and 10 physical cores
.NET SDK=7.0.304
  [Host]     : .NET 7.0.7 (7.0.723.27404), Arm64 RyuJIT AdvSIMD
  DefaultJob : .NET 7.0.7 (7.0.723.27404), Arm64 RyuJIT AdvSIMD


```
|                                     Method |    Categories |      Mean |    Error |   StdDev | Ratio | Rank |
|------------------------------------------- |-------------- |----------:|---------:|---------:|------:|-----:|
| SumOdd_BranchFree_LoopUnrolling4_Unchecked | LoopUnrolling |  81.35 ns | 0.315 ns | 0.295 ns |  0.10 |    I |
|  SumOdd_BranchFree_LoopUnrolling_Unchecked | LoopUnrolling |  84.94 ns | 0.538 ns | 0.503 ns |  0.10 |   II |
|            SumOdd_BranchFree_LoopUnrolling | LoopUnrolling |  86.71 ns | 0.943 ns | 0.882 ns |  0.10 |  III |
| SumOdd_BranchFree_LoopUnrolling_Unchecked2 | LoopUnrolling |  94.89 ns | 0.851 ns | 0.796 ns |  0.11 |   IV |
|                             SumOdd_ForEach |     Idiomatic | 102.03 ns | 0.344 ns | 0.305 ns |  0.12 |    V |
|                                SumOdd_Span |          Span | 102.86 ns | 0.520 ns | 0.435 ns |  0.12 |    V |
|                          SumOdd_BranchFree |    BranchFree | 112.14 ns | 0.704 ns | 0.659 ns |  0.13 |   VI |
|                                 SumOdd_Mod |     Idiomatic | 204.38 ns | 0.469 ns | 0.391 ns |  0.24 |  VII |
|                              SumOdd_BitAnd |     Idiomatic | 204.75 ns | 0.516 ns | 0.457 ns |  0.24 |  VII |
|                          SumOdd_LinqBitAnd |     Idiomatic | 805.39 ns | 7.803 ns | 7.299 ns |  0.96 | VIII |
|                             SumOdd_LinqMod |     Idiomatic | 841.79 ns | 6.893 ns | 6.111 ns |  1.00 |   IX |
