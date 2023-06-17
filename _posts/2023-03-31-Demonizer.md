---
title: "Demonizer"
date: 2023-03-31T23:34:30-02:00
excerpt: >-
  Demonizer is a .NET library designed to simplify the creation of console applications for demo and showcase purposes.
comments: true
share: true
show_date: true
related: true
toc: true
toc_sticky: true
categories:
  - Projects
  - Programming
tags:
  - C#
---

[![build](https://github.com/bogoware/Demonizer/actions/workflows/build.yml/badge.svg)](https://github.com/bogoware/Demonizer/actions/workflows/build.yml)
[![publish](https://github.com/bogoware/Demonizer/actions/workflows/publish.yml/badge.svg?branch=main)](https://github.com/bogoware/Demonizer/actions/workflows/publish.yml)

Demonizer is a .NET library designed to simplify the creation of console applications for demo and showcase purposes. With Demonizer, you can easily create console applications that look professional and impressive, even if you are not an expert in console application programming.

[<i class="fab fa-github" aria-hidden="true"></i>&nbsp;&nbsp;&nbsp;Source Code](https://github.com/bogoware/Demonizer/){: .btn .btn--primary .btn--small}

## Features

- **Easy-to-use API**: few lines of codes and your console app will turn into a CLI application that showcases all your demos.
- **Dependency injection**: because even a demo can evolve in something more complex to deserve a IoC.
- **spectre.console integration**: [spectre.console](https://spectreconsole.net/) is a powerful and versatile library for working with the console in .NET applications, and its integration with Demonizer makes it a great choice for creating polished and professional console demos and showcases.
  - **Rich Text Formatting**: spectre.console provides an easy-to-use API for formatting text in the console, including support for colors, styles, and fonts.
  - **Tables**: Creating tables in the console is made easy with spectre.console. You can add columns, rows, headers, and even customize borders and styles.  
  - **Interactive Prompts**: spectre.console makes it easy to prompt the user for input in the console, including support for multiple choice questions, passwords, and more.
  - **Animations and Progress Bars**: The package includes built-in support for animations and progress bars, making it easy to add some flair to your console applications.
  - **Figures**: You can also create ASCII art figures and diagrams in the console with spectre.console.
  - **And much, much more**: consult the official documentation

## Getting Started

To get started with Demonizer, simply create a project with few commands:

```shell
 dotnet new console -o MyDemoApp
 cd MyDemoApp
 dotnet add package Bogoware.Demonizer
```

then implement your first demo:

```csharp
// MyDemo.cs
using Demonizer;
using Spectre.Console;

namespace MyDemoApp;

[Demo(Name = "Hello Europe", Description = "A shiny Hello World program", Enabled = true, Order = 0)]
public class MyDemo: IDemo
{
  public void Run(string[] args) => AnsiConsole.MarkupLine("Hello [blue]Europe[/]!");
}
```

Please note that the `DemoAttribute` is not required. By default the name of the demo match its class name, its order is -1 and is enabled, of course.

Finally turn your program to a CLI application by installing Demonizer:

```csharp
// Program.cs
using Demonizer;
using MyDemoApp;

var program = new DemonizerBuilder()
 .SetAppName("My Demo App")  // Used by help
 .AddDemosFromThisAssembly() // Add all classes implementing IDemo
 .AddDemo<MyDemo>()          // Alternatively add selectively the demos you want
 .Build();

return program.Run(args);

```

Once finished you can run it from console.

**WARNING**: Dued to shell escape rules you must use '--' to signal the end of options for the `dotnet run` command and the beginning of the options to gather to the demo app under building.
This restriction doesn't apply to unrecognized subcommands of `dotnet run`.

### Screenshots

#### Default: execute all demos

![img.png](https://raw.githubusercontent.com/bogoware/Demonizer/main/assets/run.png)

#### Help switch

![img.png](https://raw.githubusercontent.com/bogoware/Demonizer/main/assets/help.png)

#### List switch

![img.png](https://raw.githubusercontent.com/bogoware/Demonizer/main/assets/list.png)

### Dependency Injection

We know, even a modest demo app can quickly require a bit of organization and DI is of help in this case.

Demonizer integrates [Microsoft Dependency Injection Extensions](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection/) to provide this capability.

Demonizer inteprets service lifetimes in the followingn way:

- **Singleton**: created once and shared by all the demos.
- **Scoped**: created once for each demo. They act like  *signletons at demo scope*.
- **Transient**: created every time they're requested.

Enabling DI in Demonizer simply imply to configure a `ServiceCollection` provided by the library:

```csharp
// Program.cs - complete example
using Demonizer;
using MyDemoApp;
using Microsoft.Extensions.DependencyInjection;

var program = new DemonizerBuilder()
    .SetAppName("My Demo App")  // Used by help
    .AddDemosFromThisAssembly() // Add all classes implementing IDemo
    .AddDemo<MyDemo>()          // Alternatively add selectively the demos you want
    .ConfigureServices(conf =>  // If your demos require DI
    {
        conf.AddSingleton<DemoSingletonService>();
        conf.AddScoped<DemoScopedService>();
        conf.AddTransient<DemoTransientService>();
    }).Build();
  
return program.Run(args);
```

## Contributing

If you would like to contribute to Demonizer, please fork the project and submit a pull request. We welcome contributions of all kinds, including bug fixes, feature requests, and documentation improvements.

## License

Demonizer is licensed under the [MIT License](https://github.com/bogoware/Demonizer/blob/main/LICENSE).
