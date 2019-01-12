---
title: Vzdálená komunikace služby v Azure Service Fabric pomocí Javy | Dokumentace Microsoftu
description: Vzdálená komunikace Service Fabric umožňuje klientů a služby musí komunikovat s služeb v Javě pomocí vzdáleného volání procedur.
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
ms.openlocfilehash: 686d736798a4d949e3590d988f399d7da82d4fee
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231984"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Vzdálená komunikace služby v Javě s využitím Reliable Services
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java v Linuxu](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Pro služby, které nejsou vázány na konkrétní komunikační protokol nebo zásobníku, jako je například WebAPI, Windows Communication Foundation (WCF) nebo jiné rozhraní modelu Reliable Services poskytuje mechanismus vzdálené komunikace rychle a snadno nastavit vzdálené volání procedur pro služby.  Tento článek popisuje, jak nastavit vzdálené volání procedur pro služby, které jsou napsané v jazyce Java.

## <a name="set-up-remoting-on-a-service"></a>Nastavování vzdálené komunikace na službu
Nastavování vzdálené komunikace služby se provádí v dva jednoduché kroky:

1. Vytvoření rozhraní pro vaši službu k implementaci. Toto rozhraní definuje metody, které jsou k dispozici pro vzdálené volání procedur pro vaši službu. Musí být metody vracející úlohy asynchronní metody. Musí implementovat rozhraní `microsoft.serviceFabric.services.remoting.Service` signál, že má služba vzdálené komunikace rozhraní.
2. Použijte naslouchací proces vzdálené komunikace v rámci služby. Jedná se `CommunicationListener` implementace, která poskytuje funkce vzdálené komunikace. `FabricTransportServiceRemotingListener` Umožňuje vytvořit naslouchací proces vzdálené komunikace pomocí výchozí přenosový protokol vzdálené komunikace.

Například následující Bezstavová služba zveřejňuje jedinou metodu k získání "Hello World" přes vzdálené volání procedury.

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
> Argumenty a návratové typy v rozhraní služby může být libovolný jednoduchý, komplexní nebo vlastní typy, ale musí být serializovatelný.
>
>

## <a name="call-remote-service-methods"></a>Volání metod vzdálené služby
Volání metod na službě s použitím vzdálené komunikace zásobníku se provádí pomocí místního proxy serveru do služby pomocí `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` třídy. `ServiceProxyBase` Metoda vytvoří místní proxy server s použitím stejného rozhraní, které služba implementuje. Pomocí proxy můžete jednoduše volat metody rozhraní vzdáleně.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

Platforma pro vzdálenou spolupráci šíří výjimek vyvolaných na službu do klienta. Takže – zpracování výjimek logiku na straně klienta s použitím `ServiceProxyBase` přímo dokáže zpracovat výjimky, které vyvolá služby.

## <a name="service-proxy-lifetime"></a>Doba platnosti služby Proxy
Vytvoření ServiceProxy je nenáročná operace, takže si můžete vytvořit tolik, je nutné. Instance Proxy služby můžete použít opakovaně, dokud se v případě potřeby zapíná. Pokud vzdálené volání procedury vyvolá výjimku, stále můžete znovu použít stejnou instanci proxy serveru. Každý ServiceProxy obsahuje komunikace klienta používaný k posílání zpráv přenosu. Při vyvolávání vzdálené volání, jsou provedeny interní kontroly k určení, zda komunikace klienta je platný. Na základě výsledků kontrol komunikaci klienta se znovu vytvořit v případě potřeby. Proto pokud dojde k výjimce, nepotřebujete znovu vytvořit `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>Doba života ServiceProxyFactory
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) je objekt factory, který vytvoří proxy pro různé vzdálené komunikace rozhraní. Pokud používáte rozhraní API `ServiceProxyBase.create` k vytváření proxy serveru, pak framework vytvoří `FabricServiceProxyFactory`.
Je vhodné vytvořit jeden ručně, když budete chtít přepsat [ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory) vlastnosti.
Objekt pro vytváření je náročná operace. `FabricServiceProxyFactory` udržuje mezipaměť komunikace klientů.
Osvědčeným postupem je do mezipaměti `FabricServiceProxyFactory` pro co nejdéle.

## <a name="remoting-exception-handling"></a>Zpracování výjimek vzdálené komunikace
Všechny vzdálené výjimky vyvolané rozhraní API služby jako runtimeexception – nebo FabricException odesílají zpět do klienta.

ServiceProxy zpracovávat všechny výjimky převzetí služeb při selhání pro službu oddíl, který je vytvořen pro. Znovu řeší koncových bodů při převzetí služeb při selhání Exceptions(Non-Transient Exceptions) a opakování volání se na správný koncový bod. Počet opakovaných pokusů pro převzetí služeb při selhání. Výjimka je nekonečno.
V případě TransientExceptions jenom znovu zkusí volání.

Výchozí parametry opakování se poskytují pomocí [OperationRetrySettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Tyto hodnoty můžete nakonfigurovat předáním objektu OperationRetrySettings ServiceProxyFactory konstruktoru.

## <a name="next-steps"></a>Další postup
* [Zabezpečení komunikace pro služby Reliable Services](service-fabric-reliable-services-secure-communication-java.md)
