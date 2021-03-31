---
title: Osvědčené postupy pro návrh aplikací pro Azure Service Fabric
description: Osvědčené postupy a faktory návrhu pro vývoj aplikací a služeb s využitím Azure Service Fabric.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: ddf846e9e3ac6add7cf3f584b702de5accfb22af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91538494"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Osvědčené postupy pro návrh aplikací pro Azure Service Fabric

Tento článek popisuje osvědčené postupy pro vytváření aplikací a služeb v Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Seznámení s Service Fabric
* Přečtěte si, jak se [chcete dozvědět o Service Fabric?](service-fabric-content-roadmap.md) článku.
* Přečtěte si o [Service Fabric scénářích aplikací](service-fabric-application-scenarios.md).
* Seznamte se s možnostmi programovacího modelu pomocí [přehledu Service Fabric přehled programovacího modelu](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Pokyny k návrhu aplikace
Seznamte se s [obecnou architekturou](/azure/architecture/reference-architectures/microservices/service-fabric) Service Fabric aplikací a jejich [hledisky návrhu](/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Zvolit bránu API
Použijte službu brány API, která komunikuje s back-end službami, které se pak dají škálovat. Používají se nejběžnější služby brány API:

- [Azure API Management](./service-fabric-api-management-overview.md), který je [integrovaný s Service Fabric](./service-fabric-tutorial-deploy-api-management.md).
- [Azure IoT Hub](../iot-hub/index.yml) nebo [Azure Event Hubs](../event-hubs/index.yml)pomocí [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) ke čtení z oddílů centra událostí.
- [Træfik reverzní proxy](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)s použitím [poskytovatele služby Azure Service Fabric](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Application Gateway Azure](../application-gateway/index.yml).

   > [!NOTE] 
   > Azure Application Gateway není přímo integrován s Service Fabric. Pro Azure API Management je obvykle Upřednostňovaná volba.
- Vlastní [ASP.NET Core](./service-fabric-reliable-services-communication-aspnetcore.md) bránu webových aplikací.

### <a name="stateless-services"></a>Bezstavové služby
Doporučujeme, abyste vždy začali vytvářet bezstavové služby pomocí [Reliable Services](./service-fabric-reliable-services-introduction.md) a ukládali stav do databáze Azure, Azure Cosmos DB nebo Azure Storage. Externě známý je známý přístup pro většinu vývojářů. Tento přístup také umožňuje využívat možnosti dotazů na Storu.  

### <a name="when-to-use-stateful-services"></a>Kdy použít stavové služby
Stavové služby zvažte, když máte scénář s nízkou latencí a potřebujete ponechat data blízko výpočtů. Mezi příklady scénářů patří i digitální vlákna IoT, herní stav, stav relace, ukládání dat do mezipaměti z databáze a dlouhotrvající pracovní postupy, které sledují volání jiných služeb.

Určete časový rámec uchovávání dat:

- **Data uložená v mezipaměti**. Použijte ukládání do mezipaměti v případě, že latence vůči externím úložištím je problém. Použijte stavovou službu jako svou vlastní datovou mezipaměť nebo zvažte použití [Open Source SoCreate Service Fabric distribuované mezipaměti](https://github.com/SoCreate/service-fabric-distributed-cache). V tomto scénáři se nemusíte zabývat, pokud ztratíte všechna data v mezipaměti.
- **Data vázaná na čas**. V takovém případě je potřeba zajistit, aby se data po určitou dobu nemusela počítat po určitou dobu, ale můžete přijít o data při *havárii*. Například v mnoha řešeních IoT je potřeba data uzavřít do výpočetní služby, například když se počítá Průměrná teplota za posledních několik dní, ale pokud dojde ke ztrátě těchto dat, konkrétní datové body nejsou důležité. V tomto scénáři také nezáleží na zálohování jednotlivých datových bodů. Zálohujete jenom vypočtené průměrné hodnoty, které se pravidelně zapisují do externího úložiště.  
- **Dlouhodobá data**. Spolehlivé kolekce můžou vaše data ukládat trvale. V tomto případě ale potřebujete připravit se na [zotavení po havárii](./service-fabric-disaster-recovery.md), včetně [Konfigurace zásad pravidelného zálohování](./service-fabric-backuprestoreservice-configure-periodic-backup.md) pro vaše clustery. V důsledku toho nakonfigurujete, co se stane, když je cluster zničený při havárii, kde byste museli vytvořit nový cluster a jak nasadit nové instance aplikace a obnovit z poslední zálohy.

Úspora nákladů a zlepšení dostupnosti:
- Pomocí stavových služeb můžete snížit náklady, protože nebudete mít přístup k datům a náklady na transakce ze vzdáleného úložiště, a proto nemusíte používat jinou službu, jako je Azure cache pro Redis.
- Používání stavových služeb primárně pro úložiště a nikoli pro výpočetní výkon je náročné a nedoporučujeme. Stavové služby můžete představit jako výpočetní prostředky s levným místním úložištěm.
- Odebráním závislostí v jiných službách můžete zlepšit dostupnost služby. Správa stavu s HA v clusteru izoluje vás od jiných výpadků služeb nebo problémů s latencí.

## <a name="how-to-work-with-reliable-services"></a>Jak pracovat s Reliable Services
Service Fabric Reliable Services vám umožní snadno vytvářet bezstavové a stavové služby. Další informace najdete v [úvodu k Reliable Services](./service-fabric-reliable-services-introduction.md).
- Vždy vyhovět [token zrušení](./service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps) v `RunAsync()` metodě pro bezstavové a stavové služby a `ChangeRole()` metodu pro stavové služby. Pokud ne, Service Fabric nevíte, jestli je možné službu zavřít. Například pokud nedodržujete token zrušení, může dojít k mnohem delší dobu upgradu aplikace.
-    Včasné otevření a ukončení [komunikačních posluchačů](./service-fabric-reliable-services-communication.md) a přijetí tokenů zrušení.
-    Nikdy nepoužívejte kombinaci synchronního kódu s asynchronním kódem. Například Nepoužívejte `.GetAwaiter().GetResult()` ve svých asynchronních voláních. Použijte asynchronní *použití v zásobníku* volání.

## <a name="how-to-work-with-reliable-actors"></a>Jak pracovat s Reliable Actors
Service Fabric Reliable Actors umožňuje snadno vytvořit stavové a virtuální objekty actor. Další informace najdete v [úvodu k Reliable Actors](./service-fabric-reliable-actors-introduction.md).

- Pro škálování vaší aplikace je vhodné zvážit použití zasílání zpráv z publikování a podsystému mezi vašimi aktéry. Mezi nástroje, které tuto službu poskytují, patří [Open-Source SoCreate Service Fabric Pub/sub](https://service-fabric-pub-sub.socreate.it/) a [Azure Service Bus](/azure/service-bus/).
- Nastavte stav objektu actor jako [podrobnějších možností](./service-fabric-reliable-actors-state-management.md#best-practices).
- Umožňuje spravovat [životní cyklus objektu actor](./service-fabric-reliable-actors-state-management.md#best-practices). Pokud je nebudete používat znovu, odstraňte objekty actor. Odstranění nepotřebných aktérů je obzvláště důležité, pokud používáte [nestálýho poskytovatele stavu](./service-fabric-reliable-actors-state-management.md#state-persistence-and-replication), protože veškerý stav je uložený v paměti.
- Vzhledem k jejich [souběžnosti](./service-fabric-reliable-actors-introduction.md#concurrency), jsou objekty actor nejlépe používány jako nezávislé objekty. Nevytvářejte grafy více objektů Actor, synchronní volání metod (každý z nich se pravděpodobně nazývá samostatné síťové volání) nebo Vytvářejte kruhové požadavky objektu actor. Tato akce významně ovlivní výkon a škálování.
- Nepoužívejte kombinaci synchronního kódu s asynchronním kódem. Používejte Async konzistentně, aby se zabránilo problémům s výkonem.
- V aktérech neprovádějte dlouhotrvající volání. Dlouhotrvající volání zablokují jiná volání stejnému objektu actor z důvodu souběžnosti založené na zapínání.
- Pokud komunikujete s jinými službami pomocí [Service Fabric vzdálené komunikace](./service-fabric-reliable-services-communication-remoting.md) a vytváříte `ServiceProxyFactory` , vytvořte továrnu na úrovni [actor-Service](./service-fabric-reliable-actors-using.md) a *ne* na úrovni objektu actor.


## <a name="application-diagnostics"></a>Application Diagnostics
Přidávejte důkladnější informace o přidávání [protokolování aplikací](./service-fabric-diagnostics-event-generation-app.md) při voláních služby. Pomůže vám diagnostikovat scénáře, ve kterých se služby vzájemně volají. Například pokud volání B volá C volá D, volání se může podařit kdekoli. Pokud nemáte dostatečné protokolování, je obtížné diagnostikovat chyby. Pokud jsou služby protokolovány příliš daleko z důvodu počtu volání, nezapomeňte alespoň protokolovat chyby a upozornění.

## <a name="iot-and-messaging-applications"></a>Aplikace IoT a zasílání zpráv
Když čtete zprávy z [azure IoT Hub](../iot-hub/index.yml) nebo [Azure Event Hubs](../event-hubs/index.yml), použijte  [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor). ServiceFabricProcessor se integruje s Service Fabric Reliable Services, aby zachoval stav čtení z oddílů centra událostí a přenáší nové zprávy do služeb prostřednictvím `IEventProcessor::ProcessEventsAsync()` metody.


## <a name="design-guidance-on-azure"></a>Pokyny k návrhu v Azure
* Pokyny k [vytváření mikroslužeb v Azure](/azure/architecture/microservices/)najdete v [centru architektury Azure](/azure/architecture/microservices/) .

* Pokyny k [používání Service Fabric v herních službách](/gaming/azure/reference-architectures/multiplayer-synchronous-sf)najdete na webu [Začínáme s Azure pro hry](/gaming/azure/) .
