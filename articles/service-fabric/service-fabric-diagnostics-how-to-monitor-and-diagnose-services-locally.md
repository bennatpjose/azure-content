<properties
   pageTitle="Microsoft Azure Service Fabric How to monitor and diagnose services locally"
   description="This article describes how you can monitor and diagnose your services written using Microsoft Azure Service Fabric on a local development machine."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/04/2015"
   ms.author="kunalds"/>


# Monitoring and Diagnosing Services in a local machine development setup
Monitoring, detecting, diagnosing and troubleshooting allows for services to continue with minimal disruption to user experience. While it is critical in an actual deployed production environment, the efficacy will depend on adopting a similar model during development of services to ensure that it works when you move to a real world setup. Service Fabric makes it easy for service developers to implement diagnostics that can seamlessly work across single machine local development and real world production cluster setups.

## Why use ETW for tracing and logging
[Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) is the recommended technology for tracing messages in Service Fabric. Reasons for this are:

* ETW is fast. It was built as a tracing technology that has a minimal impact on your code execution times.
* ETW tracing works seamlessly across local development environments and also real world cluster setups. This  means you don't have to rewrite your tracing code when you are ready to deploy your code to a real cluster.
* Service Fabric system code also uses ETW for internal tracing. This allows you to view your application traces interleaved with Service Fabric system traces, making it easier to understand the sequences and interrelationships between your application code and events in the underlying system.
* There is built-in support in Service Fabric Visual Studio tools to view ETW events.


## View Service Fabric system events in Visual Studio

Service Fabric emits ETW events to help application developers understand what is happening in the platform. Incase you haven't already done so, go ahead and follow the steps in [Creating your first application in Visual Studio](./service-fabric-create-your-first-application-in-visual-studio.md) to get an application up and running with the Diagnostics Events Viewer showing the trace messages. 

1. If the Diagnostics Events window does not automatically show, Go to Server Explorer tab in Visual Studio, right-click the Service Fabric cluster and choose "View Diagnostic Events" in the context menu.

  ![Open the Visual Studio Diagnostics Events Viewer](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/ServerExViewDiagEvents.png)

2. Each event has standard metadata information which tells you the node, application and service the event is coming from. You can also filter the list of events using the "Filter events" box at the top of the windows, for example you can filter on Node Name or Service Name. And when looking at an event's details you can also pause using the button on top of the window and resume without any loss of events at a later time.

  ![Visual Studio Diagnostics Events Viewer](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## Add your own custom traces to the application code
The Service Fabric Visual Studio project templates contain sample code. The code shows how to add custom application code ETW traces which show up in the Visual Studio ETW viewer alongside system traces from Service Fabric. The advantage of this method is that metadata is automatically added to traces, and the Visual Studio Diagnostic Viewer is already configured to display them.

For projects created from the **service templates** (stateless or stateful) just search for the `RunAsync` implementation:

1. The call to `ServiceEventSource.Current.ServiceMessage` in the `RunAsync` method shows an example of a custom ETW trace from the application code.
2. In the **ServiceEventSource.cs** file, you will find an overload for the `ServiceEventSource.ServiceMessage` method which should be used for high frequency events due to performance reasons.

For projects created from the **actor templates** (stateless or stateful):

1. Open the **"ProjectName".cs** file where *ProjectName* is the name you chose for your Visual Studio project.  
2. Find the code `ActorEventSource.Current.ActorMessage(this, "Doing Work");` in the *DoWorkAsync* method.  This is an example of a custom ETW trace written from application code.  
3. In file **ActorEventSource.cs**, you will find an overload for the `ActorEventSource.ActorMessage` method which should be used for high frequency events due to performance reasons.

After adding custom ETW tracing to your service code, you can build, deploy, and run the application again to see your event(s) in the Diagnostic Viewer. If you debug the application with F5, the Diagnostic Viewer will  open automatically.

## Next steps
The same tracing code that you added to your application above for local diagnostics will work with tools that you can use to view these events when running your application on an Azure cluster, checkout these articles that discuss the different options for the tools and describe how you can set them up.
* [Collecting logs from a Service Fabric cluster in Azure using WAD(Windows Azure Diagnostics) and Operational Insights](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md)
* [Using ElasticSearch as a Service Fabric application trace store](service-fabric-diagnostic-how-to-use-elasticsearch.md)
