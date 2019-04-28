---
title: Reliable Services WCF komunikačního balíku | Dokumentace Microsoftu
description: Integrované komunikačního balíku WCF v Service Fabric nabízí služba klienta WCF komunikaci modelu Reliable Services.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: chackdan
editor: vturecek
ms.assetid: 75516e1e-ee57-4bc7-95fe-71ec42d452b2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: ae8a0ab0382083ebfca0834d2238403668efa71d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725597"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Komunikace na základě WCF zásobníku pro služby Reliable Services
Reliable Services v rámci umožňuje autorům služby vyberte komunikační balík, které chtějí používat pro příslušnou službu. Můžete připojit v zásobníku komunikace podle vlastní volby, prostřednictvím **ICommunicationListener** vrácených [CreateServiceReplicaListeners nebo CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) metody. Rozhraní poskytuje implementaci komunikačního balíku založené na Windows Communication Foundation (WCF) pro službu autory, kteří chtějí používat komunikace na základě WCF.

## <a name="wcf-communication-listener"></a>Naslouchací proces komunikace WCF
Implementace specifické pro WCF **ICommunicationListener** je poskytován **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** třídy.

Přidejte Dejme tomu, že máme typu kontraktu služby `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Ve službě následujícím způsobem můžeme vytvořit naslouchací proces komunikace WCF.

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

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Zápis klientů pro komunikační balík WCF
Pro psaní klientům pro komunikaci se službami pomocí WCF, poskytuje rozhraní **WcfClientCommunicationFactory**, což je implementace specifické pro WCF [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Komunikační kanál WCF je přístupný z **WcfCommunicationClient** vytvořené **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Klientský kód můžete použít **WcfCommunicationClientFactory** spolu s **WcfCommunicationClient** která implementuje **ServicePartitionClient** určit koncový bod služby a komunikovat se službou.

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
> Výchozí ServicePartitionResolver předpokládá, že klient je spuštěn ve stejném clusteru jako službu. Pokud tedy tomu tak není, vytvořte objekt ServicePartitionResolver a předejte koncovým bodům připojení clusteru.
> 
> 

## <a name="next-steps"></a>Další postup
* [Volání vzdálených procedur u vzdálené komunikace modelu Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [Webové rozhraní API s OWIN v modelu Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Zabezpečení komunikace pro služby Reliable Services](service-fabric-reliable-services-secure-communication-wcf.md)

