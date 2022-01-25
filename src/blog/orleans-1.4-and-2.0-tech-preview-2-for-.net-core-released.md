Orleans 1.4 and 2.0 Tech Preview 2 for .NET Core released
=========================================================

[Julian Dominguez](https://github.com/jdom)
3/2/2017 5:54:19 PM

* * * * *

Orleans 1.4.0
=============

A few weeks ago we release Orleans 1.4.0 to NuGet.org, where the main new themes were:
- Revamped JournaledGrain for event sourcing with support for geo-distributed log-based consistency providers.
- Abstraction of Grain Services with fixed-placed per-silo application components with their workload partitioned via cluster consistency ring.
- Support for heterogeneous silos with non-uniform distribution of available grain classes.
- Cluster membership provider for Service Fabric.

Of course, there's a lot of other improvement and bug fixes, that you can read about here: [Orleans v1.4.0 release notes](https://github.com/dotnet/orleans/releases/tag/v1.4.0)

Orleans 2.0 Tech Preview 2 for .NET Core
========================================

In addition to our standard releases, we have been working in a vNext feature that supports .NET Standard (and .NET Core hosts).
Similar to TP1, this new preview is not at complete full parity with the Orleans 1.X releases, but it's getting pretty close.
We have done a lot of bug fixes since the last preview, and also this one is up to date with the latest version in our master branch (a little bit ahead of 1.4.0).

### Differences with Orleans 1.X

Some notable differences or pending things in this pre-release:

- Orleans code generation
  - Build time codegen (Microsoft.Orleans.OrleansCodeGenerator.Build nuget package) only works if building on Windows with either Visual Studio 2017 or the latest dotnet CLI.
  - Nevertheless, runtime codegen is a viable alternative that works cross-platform (by referencing Microsoft.Orleans.OrleansCodeGenerator package in the Silo host and client projects).
- BinaryFormatter (built-in .NET Serialization) is not yet available in .NET Standard, and it was being used as the default fallback serializer in Orleans (and typically used mostly when serializing exceptions). Now we have a custom IL based fallback serializer that should be fast and powerful, but might behave somewhat differently if you have existing code relying on `[Serializable]`.
- System.Diagnostic.Trace.CorrelationManager.ActivityId is not supported in .NET Standard. If you were relying on this to correlate grain calls, consider using Orleans.Runtime.RequestContext.ActivityId instead.

### Is Orleans 2.0 TP2 production ready?

Not yet.
Big disclaimer: We do our CI testing in .NET (because our tests heavily rely on AppDomains to create an in-memory cluster of silos, and those are not supported in .NET Core, but we plan to tackle that soon).
We have done some basic manual testing in .NET Core (both Windows and Linux), and we have some of our contributors using it to develop new services.
Getting feedback (and PRs!) is one of the main goals of this release, and not to be used in production yet.
Also, there is no guarantee that this technical preview is entirely backwards compatible with Orleans 1.4, even for the features that were fully ported.
Once we are closer to a stable release, we’ll list all the breaking changes that we know of in case you are interested in upgrading your application from 1.X to 2.0.

### Where to get it

Because this tech preview is not as full featured or stable as the 1.X releases is that we are only releasing in MyGet for now.
You can get the NuGet packages by following the steps to configure the feed here: [https://dotnet.myget.org/gallery/orleans-ci](https://dotnet.myget.org/gallery/orleans-ci)

### HelloWorld Sample

We now have a very simple sample in our repo that you can use to try Orleans in .NET Core (whether that's in Windows, Linux or MacOS).
The sample is located at [https://github.com/dotnet/orleans/tree/master/Samples/HelloWorld.NetCore](https://github.com/dotnet/orleans/tree/master/Samples/HelloWorld.NetCore).


Enjoy it, play with it, and lets us know what you think, either as GitHub issues, PRs or just come hang out in our Orleans 2.0 Tech Preview supporting .NET Core
=============================================

[Julian Dominguez](https://github.com/jdom)
12/5/2016 11:52:59 AM

* * * * *

It's been a long migration to have Orleans be .NET Standard compatible, but we finally have a minimum viable release ready to start playing with in .NET Core! :)

Orleans 2.0 Tech Preview 1 was just released to MyGet:
[https://dotnet.myget.org/gallery/orleans-ci](https://dotnet.myget.org/gallery/orleans-ci) (or
[https://dotnet.myget.org/F/orleans-ci/api/v3/index.json](https://dotnet.myget.org/F/orleans-ci/api/v3/index.json)
to configure the feed in NuGet)

Differences with Orleans 1.X
----------------------------

Orleans 2.0 Tech Preview is not as full featured as Orleans 1.X, as we ported the minimum needed to have a decent experience, and arguably the hardest/riskiest portion to port. We expect the rest of the extensions to be migrated much faster from now on.

Some notable differences or pending things in this pre-release:

- Orleans code generation
  - Build time codegen (Microsoft.Orleans.OrleansCodeGenerator.Build nuget package) only works if building on Windows with .NET 4.6.2 installed.
It also requires .NET Core preview3 tooling or greater (VS2017 RC if building in VS).
  - Nevertheless, runtime codegen is a viable alternative that works cross-platform (by referencing Microsoft.Orleans.OrleansCodeGenerator package in the Silo host and client projects).
- For reliable cluster membership, storage, and stream, only Azure Storage providers were migrated for now.
The rest are coming soon (or feel free to contribute a port for them).
- BinaryFormatter (built-in .NET Serialization) is not yet available in .NET Standard, and it was being used as the default fallback serializer in Orleans (and typically used mostly when serializing exceptions).
Now we have a custom IL based fallback serializer that should be fast and powerful, but might behave somewhat differently if you have existing code relying on [Serializable].
- System.Diagnostic.Trace.CorrelationManager.ActivityId is not supported in .NET Standard.
If you were relying on this to correlate grain calls, consider using Orleans.Runtime.RequestContext.ActivityId instead.

Is it production ready?
-----------------------

No.

Big disclaimer: We do our CI testing in .NET (because our tests heavily rely on AppDomains to create an in-memory cluster of silos, and those are not supported in .NET Core, but we plan to tackle that soon).
We have done some basic manual testing in .NET Core (both Windows and Linux), but expect some issues. Getting feedback (and PRs!) is one of the main goals of this release, and not to be used in production yet.

Also, there is no guarantee that this technical preview is entirely backwards compatible with Orleans 1.3, even for the features that were fully ported.
Once we are closer to a stable release, we'll list all the breaking changes that we know of in case you are interested in upgrading your application from 1.3 to 2.0.
Because this tech preview is not as full featured as the 1.X releases is that we are only release in MyGet for now.

Running sample
--------------

I created a small Hello World sample app that runs in .NET Core, and you are welcome to use it as a starting point.

The sample is located here:
[https://github.com/jdom/OrleansHelloWorldSample.Core](https://github.com/jdom/OrleansHelloWorldSample.Core)

![Sample application running Orleans in Linux](media/2016/12/linux-orleans-small-1024x576.png)
Sample application running Orleans in Linux

Enjoy it, play with it, and lets us know what you think, either as
GitHub issues, PRs or just come hang out in our [Discord chat](https://aka.ms/orleans-discord).
