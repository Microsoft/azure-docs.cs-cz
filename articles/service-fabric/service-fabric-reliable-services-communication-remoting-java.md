---
title: Vzdálená komunikace služby v Azure Service Fabric | Microsoft Docs
description: Vzdálená komunikace Service Fabric umožňuje služby a klienti komunikovat se službami s použitím vzdálené volání procedury.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 074c428662abb5c3acf86835f6fedbf3f8791acf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212972"
---
# <a name="service-remoting-with-reliable-services"></a>Služba vzdálené komunikace se službami Reliable Services
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java v Linuxu](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Spolehlivé služby framework poskytuje mechanismus vzdálenou komunikaci rychle a snadno nastavit vzdáleného volání procedur pro služby.

## <a name="set-up-remoting-on-a-service"></a>Nastavování vzdálené komunikace na službě
Nastavení vzdálené komunikace pro služby se provádí ve dvou jednoduchých kroků:

1. Vytvořte rozhraní služby k implementaci. Toto rozhraní definuje metody, které jsou k dispozici pro vzdálené volání procedury vaší služby. Metody musí být vrácení úloh asynchronních metod. Musí implementovat rozhraní `microsoft.serviceFabric.services.remoting.Service` signál, že služba má vzdálené komunikace rozhraní.
2. Použijte vzdálenou komunikaci naslouchací proces ve službě. Jedná se `CommunicationListener` implementace, která poskytuje funkce vzdálené komunikace. `FabricTransportServiceRemotingListener` Umožňuje vytvořit naslouchací proces vzdálenou komunikaci pomocí protokolu přenosu výchozí vzdálenou komunikaci.

Například následující bezstavové služby zpřístupní jedné metody přes vzdálené volání procedury získat "Hello World".

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> Argumenty a návratové typy v rozhraní služby můžou být všechny typy jednoduchý, komplexní nebo vlastní, ale musí být serializovatelný.
>
>

## <a name="call-remote-service-methods"></a>Volání metody vzdálené služby
Volání metod pro službu pomocí vzdálené komunikace zásobníku se provádí pomocí místní proxy server a služby pomocí `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` třídy. `ServiceProxyBase` Metoda vytvoří místní proxy server pomocí stejné rozhraní, který implementuje službu. Pomocí proxy můžete jednoduše volat metody na rozhraní vzdáleně.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

Vzdálená komunikace framework rozšíří výjimek vyvolaných ve službách do klienta. Logika tak výjimek na straně klienta pomocí `ServiceProxyBase` můžete přímo zpracování výjimek, které vyvolá službu.

## <a name="service-proxy-lifetime"></a>Doby platnosti Proxy služby
Vytvoření ServiceProxy je lightweight operace, takže uživatel může vytvořit tolik, jako je potřebují. Proxy server služby lze znovu použít, dokud ho uživatel potřebovat. Uživatel může znovu použít stejné proxy serveru v případě výjimky. Každý ServiceProxy obsahuje komunikace klienta používá k odeslání zprávy prostřednictvím sítě. Při volání rozhraní API, máme interní zkontrolujte, jestli je komunikace klienta použít platný. Podle toho, že výsledků, znovu vytvoříme komunikace klienta. Uživatel proto není potřeba znovu vytvořit serviceproxy v případě výjimky.

### <a name="serviceproxyfactory-lifetime"></a>Doba platnosti ServiceProxyFactory
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client._fabric_service_proxy_factory) je objekt factory, který vytvoří proxy server pro různé vzdálené komunikace rozhraní. Pokud používáte rozhraní API `ServiceProxyBase.create` k vytváření proxy serveru, pak framework vytvoří `FabricServiceProxyFactory`.
Je vhodné vytvořit jednu ručně, až budete potřebovat k přepsání [ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client._service_remoting_client_factory) vlastnosti.
Objekt Factory je náročná operace. `FabricServiceProxyFactory` udržuje mezipaměť komunikace klientů.
Osvědčeným postupem je do mezipaměti `FabricServiceProxyFactory` jako dlouho.

## <a name="remoting-exception-handling"></a>Vzdálená komunikace zpracování výjimek
Všechny vzdálené výjimky vyvolané rozhraní API služby, se odesílají zpět do klienta jako runtimeexception – nebo FabricException.

ServiceProxy zpracovávat všechny výjimky převzetí služeb při selhání pro oddíl služby, kterou je vytvořeno. Znovu přeloží koncových bodů při převzetí služeb při selhání Exceptions(Non-Transient Exceptions) a opakuje volání s správný koncový bod. Počet opakovaných pokusů pro převzetí služeb při selhání výjimka je neomezené.
V případě TransientExceptions se pouze pokusí volání.

Výchozí parametry opakování se poskytují podle [OperationRetrySettings]. (https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client._operation_retry_settings) Uživatel může konfigurovat tyto hodnoty pomocí předání objektu OperationRetrySettings ServiceProxyFactory konstruktor.

## <a name="next-steps"></a>Další postup
* [Zabezpečení komunikace pro spolehlivé služby](service-fabric-reliable-services-secure-communication.md)
