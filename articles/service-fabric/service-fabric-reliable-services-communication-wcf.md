---
title: Reliable Services komunikačního zásobníku WCF
description: Integrovaný komunikační zásobník WCF v Service Fabric poskytuje komunikaci služby WCF klienta s Reliable Services.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: c5b5d413eee2528e2d5c7d04d06f1607949beaae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89012700"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Komunikační zásobník založený na WCF pro Reliable Services
Rozhraní Reliable Services Framework umožňuje autorům služeb zvolit komunikační zásobník, který chtějí používat pro své služby. Mohou připojit komunikační zásobník podle svého výběru prostřednictvím **ICommunicationListener** vráceného z metod [CreateServiceReplicaListeners nebo CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) . Rozhraní poskytuje implementaci komunikačního zásobníku na základě Windows Communication Foundation (WCF) pro autory služeb, kteří chtějí používat komunikaci založenou na technologii WCF.

## <a name="wcf-communication-listener"></a>Naslouchací proces komunikace WCF
Implementace **ICommunicationListener** specifická pro WCF je poskytována třídou **Microsoft. ServiceFabric. Services. Communication. WCF. Runtime. WcfCommunicationListener** .

Lest řekněme, že máme kontrakt služby typu `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

V rámci služby můžeme následujícím způsobem vytvořit naslouchací proces komunikace WCF.

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
Pro psaní klientů ke komunikaci se službami pomocí technologie WCF poskytuje rozhraní **WcfClientCommunicationFactory**, což je implementace [CLIENTCOMMUNICATIONFACTORYBASE](service-fabric-reliable-services-communication.md)specifická pro WCF.

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Na komunikační kanál WCF se dá dostat z **WcfCommunicationClient** vytvořeného pomocí **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Klientský kód může použít **WcfCommunicationClientFactory** spolu s **WcfCommunicationClient** , který implementuje **ServicePartitionClient** pro určení koncového bodu služby a komunikace se službou.

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
> Výchozí ServicePartitionResolver předpokládá, že klient je spuštěný ve stejném clusteru jako služba. V takovém případě vytvořte objekt ServicePartitionResolver a předejte ho koncovým bodům připojení clusteru.
> 
> 

## <a name="next-steps"></a>Další kroky
* [Vzdálené volání procedur s Reliable Services vzdálené komunikace](service-fabric-reliable-services-communication-remoting.md)
* [Webové rozhraní API s OWIN v Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Zabezpečení komunikace pro Reliable Services](service-fabric-reliable-services-secure-communication-wcf.md)
