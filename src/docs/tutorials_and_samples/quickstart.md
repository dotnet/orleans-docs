---
title: Servers
---

# Quick start: creating an Orleans application

In an empty directory, create a new console application using the [.NET CLI](https://docs.microsoft.com/dotnet/core/tools/):

``` powershell
dotnet new console
```

Add the following packages to your application:

* [`Microsoft.Extensions.Hosting`](https://www.nuget.org/packages/Microsoft.Extensions.Hosting), which contains the [.NET Generic Host](https://docs.microsoft.com/dotnet/core/extensions/generic-host)
* [`Microsoft.Orleans.Server`](https://www.nuget.org/packages/Microsoft.Orleans.Server), which includes the Orleans runtime as well as some included plugins
* [`Microsoft.Orleans.CodeGenerator.MSBuild`](https://www.nuget.org/packages/Microsoft.Orleans.CodeGenerator.MSBuild), which includes Orleans' [code generator](~/docs/grains/code_generation.md)

``` powershell
dotnet add package Microsoft.Extensions.Hosting
dotnet add package Microsoft.Orleans.Server
dotnet add package Microsoft.Orleans.CodeGenerator.MSBuild
```

Overwrite `Program.cs` with the following, which creates a new `HostBuilder` using `Host.CreateDefaultBuilder()` and configures it with Orleans using the `UseOrleans` extension method:

``` csharp
using Microsoft.Extensions.Hosting;
using Orleans.Hosting;

using var host = await Host.CreateDefaultBuilder(args)
    .UseOrleans(siloBuilder => siloBuilder.UseLocalhostClustering())
    .StartAsync();

await host.WaitForShutdownAsync();
```

Run your application and observe that logs are printed to the console:

``` powershell
dotnet run
```

Press Ctrl+C to stop the application.

Our application does nothing useful yet.
To change that, we can add a new [grain interface](~/docs/grains/grain-interfaces.md) and a [grain class](~/docs/grains/grain-classes.md) which implements it.

Add the following using directives to the top of `Program.cs`:

``` csharp
using System.Threading.Tasks;
using Orleans;
```

Next, add a grain interface and then an implementation of that interface at the bottom of `Program.cs`:

``` csharp
public interface IGreeterGrain : IGrainWithStringKey
{
    Task<string> GetGreeting(string name);
}

public class GreeterGrain : Grain, IGreeterGrain
{
    public Task<string> GetGreeting(string name) => Task.FromResult($"Hi, {name}!");
}
```

We now have a grain in our program.
The next step is to interact with the grain.
We need to wait until Orleans has started before we interact with any grain.
Add the following above the `await host.WaitForShutdownAsync()` statement in `Program.cs` to the following:

``` csharp
// Get the client from the host's service provider
var client = host.Services.GetService<IGrainFactory>();

// Get a reference to a grain
var myGrain = client.GetGrain<IGreeterGrain>("Bob");

// Call the grain
var result = await myGrain.GetGreeting("Joy");

// Print the result
Console.WriteLine($"Result: {result}");
```

The completed `Program.cs` file should look like this:

``` csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Orleans;
using Orleans.Hosting;

// Start a new host configured with Orleans
using var host = await Host.CreateDefaultBuilder(args)
    .UseOrleans(siloBuilder => siloBuilder.UseLocalhostClustering())
    .StartAsync();

// Get the client from the host's service provider
var client = host.Services.GetService<IGrainFactory>();

// Get a reference to a grain
var myGrain = client.GetGrain<IGreeterGrain>("Bob");

// Call the grain
var result = await myGrain.GetGreeting("Joy");

// Print the result
Console.WriteLine($"Result: {result}");

await host.WaitForShutdownAsync();

public interface IGreeterGrain : IGrainWithStringKey
{
    Task<string> GetGreeting(string name);
}

public class GreeterGrain : Grain, IGreeterGrain
{
    public Task<string> GetGreeting(string name) => Task.FromResult($"Hi, {name}!");
}
```

Run the program again using `dotnet run` and see that `Result: Hi, Joy!` is printed to the console.
