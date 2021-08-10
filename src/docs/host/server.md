---
title: Servers
---

# Servers

Orleans applications consist of processes taking on one or two roles:

* **Servers**, also known as *silos*, which host your grains on the *Orleans runtime*
* **Clients**, which connect to servers and issue requests to grains in the form of method calls

All Orleans applications must have at least one server process since that is where grain code executes.
All server processes are also clients: they are able to issue requests to grains.
Separate client processes are optional in Orleans.

In addition to hosting grains, servers also host everything necessary for grains to be able to execute reliably and efficiently.
This includes coordinating with each other to share load and other responsibilities such as monitoring each other to recover from faults.

## Silos

In Orleans, the server is also known as a silo since it is a container for grains.
The name comes from agriculture, where a [silo](https://wikipedia.org/wiki/Silo) is a structure which holds grains.
We use the terms *server* and *silo* interchangeably.

## Hosting

Orleans relies on [.NET Generic Host](https://docs.microsoft.com/dotnet/core/extensions/generic-host).
This host provides features used by many .NET application frameworks, such as:

* [Dependency Injection (DI)](https://docs.microsoft.com/dotnet/core/extensions/dependency-injection)
* [Logging](https://docs.microsoft.com/dotnet/core/extensions/logging)
* [Configuration](https://docs.microsoft.com/dotnet/core/extensions/configuration)
* [Application lifetime](https://docs.microsoft.com/dotnet/core/extensions/generic-host#ihostapplicationlifetime) for handling application startup and shutdown, including Ctrl+C/`SIGINT`, Windows & systemd service lifecycle
* Hosted services (implementations of [`IHostedService`](https://docs.microsoft.com/dotnet/api/microsoft.extensions.hosting.ihostedservicehttps://github.com/dotnet/orleans-docs/blob/main/src/docs/deployment/kubernetes.md))

Orleans provides an extension method, `IHostBuilder.UseOrleans`, which adds an Orleans server as an `IHostedService` and accepts a delegate to configure an `ISiloBuilder` which contains Orleans-specific configuration methods.
Other .NET frameworks also support .NET Generic Host and it's common for a single application to consist of multiple framework components, such as Orleans, ASP.NET MVC, SignalR, and gRPC.
This makes it straightforward to add Orleans to an existing ASP.NET application.

Orleans does not override anything which is provided by .NET Generic Host, so any existing knowledge you have about .NET Dependency Injection, Logging, etcetera all applies to Orleans.

## Clusters

The server processes which make up your Orleans application communicate with each other via the network, forming a [*cluster*](https://wikipedia.org/wiki/Computer_cluster).
A cluster is a group of servers which work together and share responsibilities.
For example, Orleans distributes your grains across the cluster of servers.
This allows you to scale your application's capacity on the fly by adding more servers or removing servers.
If a server fails, Orleans will detect that and will remove it from the cluster.
The cluster maintains a list of the servers which are members of the cluster through a process known as *cluster membership*, often referred to simply as *clustering*.

Cluster membership in Orleans uses plugins which interface with a shared database in order to reliably store cluster membership information.
Microsoft and the community have produced cluster membership plugins for many systems, including:

* [Redis](https://www.nuget.org/packages/Orleans.Clustering.Redis/)
* [Azure Cosmos DB](https://www.nuget.org/packages/Orleans.Clustering.CosmosDB/)
* [Azure Table Storage](https://www.nuget.org/packages/Microsoft.Orleans.Clustering.AzureStorage/)
* [SQL Server, Postgres, MySQL, and other providers](https://www.nuget.org/packages/Microsoft.Orleans.Clustering.AdoNet/) supported by [ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/)
* [ZooKeeper](https://www.nuget.org/packages/Microsoft.Orleans.OrleansZooKeeperUtils/)
* [Consul](https://www.nuget.org/packages/Microsoft.Orleans.OrleansConsulUtils/)
* [Kubernetes CRDs](https://www.nuget.org/packages/Orleans.Clustering.Kubernetes/)
* In-memory (available as `UseDevelopmentClustering`/`UseLocalhostClustering`, development purposes only)

> [!WARNING]
> Do not use the in-memory clustering provider in a production environment. It is designed only for development and testing scenarios.
