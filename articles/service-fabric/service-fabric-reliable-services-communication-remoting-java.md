---
title: Vzdálená komunikace služby pomocí Java v Azure Service Fabric
description: Vzdálená komunikace Service Fabric umožňuje klientům a službám komunikovat se službami Java pomocí vzdáleného volání procedur.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: d53d20510db70d81aab796efab48de40c880bb3a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87316120"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Vzdálená komunikace služby v jazyce Java s Reliable Services
> [!div class="op_single_selector"]
> * [C# v systému Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java v Linuxu](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Pro služby, které nejsou vázané na konkrétní komunikační protokol ani zásobník, jako je WebAPI, Windows Communication Foundation (WCF) nebo jiné, Reliable Services Framework poskytuje mechanismus vzdálené komunikace pro rychlé a snadné nastavení vzdálených volání procedur pro služby.  Tento článek popisuje, jak nastavit vzdálená volání procedur pro služby napsané v jazyce Java.

## <a name="set-up-remoting-on-a-service"></a>Nastavení vzdálené komunikace u služby
Nastavení vzdálené komunikace pro službu se provádí ve dvou jednoduchých krocích:

1. Vytvořte rozhraní pro implementaci služby. Toto rozhraní definuje metody, které jsou k dispozici pro vzdálené volání procedur ve vaší službě. Metody musí být asynchronní metody vracející úlohy. Rozhraní musí implementovat `microsoft.serviceFabric.services.remoting.Service` k signalizaci, že služba má rozhraní vzdálené komunikace.
2. V rámci služby použijte naslouchací proces vzdálené komunikace. Toto je `CommunicationListener` implementace, která poskytuje možnosti vzdálené komunikace. `FabricTransportServiceRemotingListener` dá se použít k vytvoření naslouchacího procesu vzdálené komunikace pomocí výchozího přenosového protokolu vzdálené komunikace.

Například následující Bezstavová služba zpřístupňuje jedinou metodu pro získání "Hello World" prostřednictvím vzdáleného volání procedury.

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
> Argumenty a návratové typy v rozhraní služby můžou být jakékoli jednoduché, komplexní nebo vlastní typy, ale musí být serializovatelné.
>
>

## <a name="call-remote-service-methods"></a>Volání metod vzdálené služby
Volání metod na službu pomocí zásobníku vzdálené komunikace se provádí pomocí místního proxy serveru přes `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` třídu. `ServiceProxyBase`Metoda vytvoří místní proxy server pomocí stejného rozhraní, které služba implementuje. Pomocí tohoto proxy serveru můžete jednoduše volat metody na rozhraní vzdáleně.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

Rozhraní vzdálené komunikace šíří výjimky vyvolané ve službě klientovi. Proto logika zpracování výjimek v klientovi pomocí `ServiceProxyBase` může přímo zpracovat výjimky, které služba vyvolá.

## <a name="service-proxy-lifetime"></a>Doba života proxy služby
Vytváření ServiceProxy je odlehčená operace, takže můžete vytvořit tolik, kolik potřebujete. Instance proxy služby se dají znovu použít, pokud jsou potřeba. Pokud vzdálené volání procedury vyvolá výjimku, můžete přesto použít stejnou instanci proxy. Každý ServiceProxy obsahuje komunikačního klienta, který slouží k posílání zpráv přes drát. Při vyvolání vzdálených volání se provádí interní kontroly, které určují, jestli je komunikační klient platný. V závislosti na výsledcích těchto kontrol se komunikační klient v případě potřeby znovu vytvoří. Proto pokud dojde k výjimce, není nutné znovu vytvořit `ServiceProxy` .

### <a name="serviceproxyfactory-lifetime"></a>Doba života ServiceProxyFactory
[FabricServiceProxyFactory](/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) je objekt pro vytváření, který vytváří proxy pro různá rozhraní pro vzdálenou komunikaci. Pokud používáte rozhraní API `ServiceProxyBase.create` pro vytvoření proxy serveru, pak rozhraní vytvoří `FabricServiceProxyFactory` .
Je vhodné ho vytvořit ručně, když potřebujete přepsat vlastnosti [ServiceRemotingClientFactory](/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory) .
Továrna je náročná operace. `FabricServiceProxyFactory` udržuje mezipaměť komunikačních klientů.
Osvědčeným postupem je ukládání do mezipaměti `FabricServiceProxyFactory` , pokud je to možné.

## <a name="remoting-exception-handling"></a>Zpracování výjimek vzdálené komunikace
Veškerá vzdálená výjimka vyvolaná rozhraním API služby se pošle zpátky klientovi buď jako RuntimeException – nebo FabricException.

ServiceProxy zpracovává veškerou výjimku převzetí služeb při selhání pro oddíl služby, pro který je vytvořen. Pokud dojde k převzetí služeb při selhání (nepřechodnými výjimkami) a opakování volání se správným koncovým bodem, znovu se přeloží koncové body. Počet opakovaných pokusů pro výjimku převzetí služeb při selhání je nekonečný.
V případě TransientExceptions se znovu pokusí zavolat.

Výchozí parametry opakování jsou nechte pomocí [OperationRetrySettings](/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Tyto hodnoty můžete nakonfigurovat předáním objektu OperationRetrySettings do konstruktoru ServiceProxyFactory.

## <a name="next-steps"></a>Další kroky
* [Zabezpečení komunikace pro Reliable Services](service-fabric-reliable-services-secure-communication-java.md)
