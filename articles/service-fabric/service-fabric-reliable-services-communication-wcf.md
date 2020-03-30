---
title: Spolehlivé služby WCF komunikační zásobník
description: Integrovaný zásobník komunikace WCF v service fabric poskytuje komunikaci WCF klientských služeb pro spolehlivé služby.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: 7f3b3974893316a488270f755b8f8822080658d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433867"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Komunikační zásobník založený na WCF pro spolehlivé služby
Rozhraní spolehlivé služby umožňuje autorům služby zvolit zásobník komunikace, který chtějí použít pro své služby. Mohou připojit komunikační zásobník podle svého výběru prostřednictvím **ICommunicationListener** vrácené z [CreateServiceReplicaListeners nebo CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) metody. Rámec poskytuje implementaci zásobníku komunikace založené na Windows Communication Foundation (WCF) pro autory služeb, kteří chtějí používat komunikaci založenou na WCF.

## <a name="wcf-communication-listener"></a>Naslouchací proces komunikace WCF
Implementace **ICommunicationListener** specifická pro WCF je poskytována třídou **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener.**

Abychom neřekli, že máme smlouvu o poskytování služeb typu`ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Můžeme vytvořit naslouchací proces komunikace WCF ve službě následujícím způsobem.

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

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Zápis klientů pro komunikační zásobník WCF
Pro zápis klientů ke komunikaci se službami pomocí WCF poskytuje rozhraní **WcfClientCommunicationFactory**, což je implementace [ClientCommunicationFactory .](service-fabric-reliable-services-communication.md)

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Komunikační kanál WCF lze přistupovat z **WcfCommunicationClient** vytvořené **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Klientský kód můžete použít **WcfCommunicationClientFactory** spolu s **WcfCommunicationClient,** který implementuje **ServicePartitionClient** k určení koncového bodu služby a komunikovat se službou.

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
> Výchozí servicepartitionresolver předpokládá, že klient běží ve stejném clusteru jako služba. Pokud tomu tak není, vytvořte objekt ServicePartitionResolver a předajte koncové body připojení clusteru.
> 
> 

## <a name="next-steps"></a>Další kroky
* [Vzdálené volání procedur s vzdálenou funkcí služby](service-fabric-reliable-services-communication-remoting.md)
* [Webové rozhraní API s OWIN ve spolehlivých službách](service-fabric-reliable-services-communication-webapi.md)
* [Zajištění komunikace pro spolehlivé služby](service-fabric-reliable-services-secure-communication-wcf.md)

