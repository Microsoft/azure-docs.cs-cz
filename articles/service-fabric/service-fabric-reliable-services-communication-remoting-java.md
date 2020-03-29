---
title: Vzdálené komunikace služby pomocí Javy ve službě Azure Service Fabric
description: Vzdálené komunikace Service Fabric umožňuje klientům a službám komunikovat se službami Java pomocí vzdáleného volání procedur.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: eef63d7a2c8a4b15938dfbffd7db5f9d1b22d426
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426641"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Vzdálená komunikace služby v Javě se spolehlivými službami
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java v Linuxu](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Pro služby, které nejsou vázány na konkrétní komunikační protokol nebo zásobník, jako je například WebAPI, Windows Communication Foundation (WCF) nebo jiné, poskytuje rozhraní spolehlivé služby mechanismus vzdálené komunikace pro rychlé a snadné nastavení vzdálených volání procedur Služby.  Tento článek popisuje, jak nastavit vzdálené procedury volání pro služby napsané s Javou.

## <a name="set-up-remoting-on-a-service"></a>Nastavení vzdálené komunikace ve službě
Nastavení vzdálené komunikace pro službu se provádí ve dvou jednoduchých krocích:

1. Vytvořte rozhraní pro vaši službu k implementaci. Toto rozhraní definuje metody, které jsou k dispozici pro vzdálené volání procedury ve vaší službě. Metody musí být úlohy vracející asynchronní metody. Rozhraní musí `microsoft.serviceFabric.services.remoting.Service` implementovat signál, že služba má rozhraní vzdálené komunikace.
2. Ve své službě použijte posluchače vzdálené komunikace. Toto `CommunicationListener` je implementace, která poskytuje možnosti vzdálené komunikace. `FabricTransportServiceRemotingListener`lze vytvořit vzdálené houslového procesu pomocí výchozího protokolu přenosu vzdálené komunikace.

Například následující bezstavová služba zpřístupňuje jednu metodu získat "Hello World" přes vzdálené volání procedury.

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
> Argumenty a návratové typy v rozhraní služby může být všechny jednoduché, složité nebo vlastní typy, ale musí být serializovatelné.
>
>

## <a name="call-remote-service-methods"></a>Volání metod vzdálené služby
Volání metod ve službě pomocí zásobníku vzdálené komunikace se provádí pomocí místního proxy služby prostřednictvím `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` třídy. Metoda `ServiceProxyBase` vytvoří místní proxy server pomocí stejnérozhraní, které implementuje služba. S tímto proxy, můžete jednoduše volat metody na rozhraní na dálku.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

Rozhraní vzdálené komunikace šíří výjimky vyvoláné ve službě klientovi. Logika zpracování výjimek na `ServiceProxyBase` straně klienta pomocí můžete přímo zpracovat výjimky, které služba vyvolá.

## <a name="service-proxy-lifetime"></a>Životnost proxy serveru služby
ServiceProxy vytvoření je zjednodušená operace, takže můžete vytvořit tolik, kolik potřebujete. Instance proxy služby lze znovu použít tak dlouho, jak je potřeba. Pokud vzdálené volání procedury vyvolá výjimku, můžete stále znovu použít stejnou instanci proxy. Každý ServiceProxy obsahuje komunikační ho klienta, který slouží k odesílání zpráv po drátě. Při vyvolání vzdálených volání se provádějí interní kontroly, aby se zjistilo, zda je komunikační klient platný. Na základě výsledků těchto kontrol je komunikační klient v případě potřeby znovu vytvořen. Proto pokud dojde k výjimce, není `ServiceProxy`nutné znovu vytvořit .

### <a name="serviceproxyfactory-lifetime"></a>Životnost serviceproxyfactory
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) je továrna, která vytváří proxy pro různá rozhraní vzdálené komunikace. Pokud používáte `ServiceProxyBase.create` rozhraní API pro vytváření `FabricServiceProxyFactory`proxy, pak framework vytvoří .
Je užitečné vytvořit ručně, když potřebujete přepsat [ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory) vlastnosti.
Továrna je nákladná operace. `FabricServiceProxyFactory`udržuje mezipaměť komunikačních klientů.
Osvědčeným postupem `FabricServiceProxyFactory` je do mezipaměti tak dlouho, jak je to možné.

## <a name="remoting-exception-handling"></a>Zpracování výjimek vzdálené komunikace
Všechny vzdálené výjimky vyvolány rozhraní masy služby, jsou odesílány zpět klientovi buď jako RuntimeException nebo FabricException.

ServiceProxy zpracovává všechny výjimky převzetí služeb při selhání pro oddíl služby, pro který je vytvořen. Znovu vyřeší koncové body, pokud existuje výjimky převzetí služeb při selhání (nepřechodné výjimky) a opakuje volání se správným koncovým bodem. Počet opakovaných pokusů o převzetí služeb při selhání Výjimka je neomezená.
V případě Přechodové výjimky pouze opakuje volání.

Výchozí parametry opakování jsou provied podle [OperationRetrySettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Tyto hodnoty můžete nakonfigurovat předáním objektu OperationRetrySettings konstruktoru ServiceProxyFactory.

## <a name="next-steps"></a>Další kroky
* [Zajištění komunikace pro spolehlivé služby](service-fabric-reliable-services-secure-communication-java.md)
