---
title: Spolehlivé služby WCF komunikačního balíku | Microsoft Docs
description: Předdefinované komunikačního balíku WCF v Service Fabric poskytuje služba klienta WCF komunikaci pro spolehlivé služby.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 75516e1e-ee57-4bc7-95fe-71ec42d452b2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: f5ca579b446e5d3608d53cea73fa9392cd00db06
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212220"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Komunikace na základě WCF zásobníku pro spolehlivé služby
Spolehlivé služby framework umožňuje autorům služby vyberte komunikačního balíku, který chtějí používat pro své služby. Můžete zařadit komunikačního balíku libovolný prostřednictvím **ICommunicationListener** vrácená z [CreateServiceReplicaListeners nebo CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) metody. Rozhraní framework poskytuje implementaci komunikačního balíku založené na Windows Communication Foundation (WCF) pro autory služby, které chtějí využívat komunikace na základě WCF.

## <a name="wcf-communication-listener"></a>Naslouchací proces komunikace WCF
Implementace WCF specifické **ICommunicationListener** zajišťuje **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** třídy.

Přidejte vyslovte máme kontraktu služby typu `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Můžeme vytvořit naslouchací proces WCF komunikace v rámci služby má následující omezení.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Zápis klientů pro komunikačního balíku WCF
Pro psaní klienty komunikovat se službami pomocí WCF, poskytuje rozhraní **WcfClientCommunicationFactory**, což je WCF konkrétní implementace [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Komunikační kanál WCF je přístupná z **WcfCommunicationClient** vytvořené **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Kód klienta můžete použít **WcfCommunicationClientFactory** spolu s **WcfCommunicationClient** které implementuje **ServicePartitionClient** zjistit koncový bod služby a komunikovat se službou.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> Výchozí ServicePartitionResolver předpokládá, že klient běží ve stejném clusteru jako služba. Pokud je to tak není, vytvořit objekt ServicePartitionResolver a předat koncové body připojení clusteru.
> 
> 

## <a name="next-steps"></a>Další postup
* [Vzdálené volání procedur u vzdálené komunikace spolehlivé služby](service-fabric-reliable-services-communication-remoting.md)
* [Webové rozhraní API s OWIN v spolehlivé služby](service-fabric-reliable-services-communication-webapi.md)
* [Zabezpečení komunikace pro spolehlivé služby](service-fabric-reliable-services-secure-communication.md)

