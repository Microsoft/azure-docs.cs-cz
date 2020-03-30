---
title: Osvědčené postupy návrhu aplikace Azure Service Fabric
description: Osvědčené postupy a aspekty návrhu pro vývoj aplikací a služeb pomocí Azure Service Fabric.
author: markfussell
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: mfussell
ms.openlocfilehash: 876980bd6a59bace9ab4e490358964d19fa52c7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586083"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Osvědčené postupy návrhu aplikace Azure Service Fabric

Tento článek obsahuje pokyny pro osvědčené postupy pro vytváření aplikací a služeb na Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Seznamte se s service fabric
* Přečtěte si [článek So you want to learn about Service Fabric?](service-fabric-content-roadmap.md)
* Přečtěte si o [scénářích aplikace Service Fabric](service-fabric-application-scenarios.md).
* Seznamte se s volbami programovacího modelu čtením [přehledu programovacího modelu Service Fabric](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Pokyny k návrhu aplikace
Seznamte se s [obecnou architekturou](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) aplikací Service Fabric a jejich [aspekty návrhu](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Výběr brány rozhraní API
Použijte službu brány rozhraní API, která komunikuje s back-endové služby, které pak lze škálovat. Nejběžnější služby brány rozhraní API jsou:

- [Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), který je [integrovaný s Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) nebo [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)pomocí [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) ke čtení z oddílů Centra událostí.
- [Reverzní proxy server Træfik](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)pomocí [zprostředkovatele Azure Service Fabric](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Brána aplikací Azure](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Azure Application Gateway není přímo integrovaná s Service Fabric. Azure API Management je obvykle upřednostňovanou volbou.
- Vlastní bránu webových aplikací [ASP.NET Core.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore)

### <a name="stateless-services"></a>Služby bez státní příslušnosti
Doporučujeme, abyste vždy začali vytvářením bezstavových služeb pomocí [spolehlivých služeb](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) a ukládáním stavu v databázi Azure, Azure Cosmos DB nebo Azure Storage. Externalizovaný stav je známější přístup pro většinu vývojářů. Tento přístup také umožňuje využít možnosti dotazu v úložišti.  

### <a name="when-to-use-stateful-services"></a>Kdy používat stavové služby
Zvažte stavové služby, když máte scénář pro nízkou latenci a potřebujete zachovat data v blízkosti výpočetního prostředí. Některé příklady scénářů zahrnují zařízení s digitálními dvojčaty IoT, stav hry, stav relace, ukládání dat do mezipaměti z databáze a dlouhotrvající pracovní postupy pro sledování volání jiných služeb.

Rozhodněte o časovém rámci uchovávání dat:

- **Data uložená v mezipaměti**. Použití ukládání do mezipaměti v případě, že latence na externí úložiště je problém. Použijte stavovou službu jako vlastní mezipaměť dat nebo zvažte použití [distribuované mezipaměti socreate service fabric s otevřeným zdrojovým kódem](https://github.com/SoCreate/service-fabric-distributed-cache). V tomto scénáři není nutné se obávat, pokud ztratíte všechna data v mezipaměti.
- **Časově vázaná data**. V tomto scénáři je třeba zachovat data blízko výpočetní po určitou dobu latence, ale můžete si dovolit ztratit data v *případě havárie*. Například v mnoha řešeních IoT musí být data blízko výpočetním prostředkům, například při výpočtu průměrné teploty za posledních několik dní, ale pokud dojde ke ztrátě těchto dat, konkrétní zaznamenané datové body nejsou tak důležité. Také v tomto scénáři se obvykle nestaráte o zálohování jednotlivých datových bodů. Zálohovat pouze vypočítané průměrné hodnoty, které jsou pravidelně zapisovány do externího úložiště.  
- **Dlouhodobé údaje**. Spolehlivé kolekce mohou ukládat vaše data trvale. Ale v tomto případě je třeba [se připravit na zotavení po havárii](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery), včetně konfigurace zásad [pravidelného zálohování](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) pro vaše clustery. Ve skutečnosti nakonfigurujete, co se stane, pokud je váš cluster zničen při havárii, kde budete muset vytvořit nový cluster a jak nasadit nové instance aplikací a obnovit z nejnovější zálohy.

Ušetřete náklady a zlepšete dostupnost:
- Můžete snížit náklady pomocí stavové služby, protože vám nevznikají náklady na přístup k datům a transakce ze vzdáleného úložiště a protože nemusíte používat jinou službu, jako je Azure Cache pro Redis.
- Použití stavových služeb především pro úložiště a ne pro výpočetní prostředky je nákladné a nedoporučujeme to. Myslete na stavové služby jako výpočetní prostředky s levným místním úložištěm.
- Odebráním závislostí na jiných službách můžete zlepšit dostupnost služeb. Správa stavu s HA v clusteru vás izoluje od jiných prostojů služby nebo problémů s latencí.

## <a name="how-to-work-with-reliable-services"></a>Jak pracovat se spolehlivými službami
Service Fabric Spolehlivé služby umožňuje snadno vytvářet bezstavové a stavové služby. Další informace naleznete v [úvodu služby spolehlivé](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Vždy ctít [zrušení](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) token `RunAsync()` v metodě pro bezstavové a stavové služby a metodu `ChangeRole()` pro stavové služby. Pokud tak nechcete, Service Fabric neví, pokud vaše služba může být uzavřena. Například pokud nectíte token zrušení, může dojít k mnohem delším časům upgradu aplikace.
-   Otevřete a zavřete [posluchače komunikace](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) včas a ctít tokeny zrušení.
-   Nikdy nekombinujte synchronizační kód s asynchronním kódem. Nepoužívejte `.GetAwaiter().GetResult()` například v asynchronních hovorech. Používejte asynchronní *celou cestu* přes zásobník volání.

## <a name="how-to-work-with-reliable-actors"></a>Jak pracovat se spolehlivými aktéry
Service Fabric Reliable Actors umožňuje snadno vytvářet stavové virtuální objekty actor. Další informace naleznete v [úvodu spolehlivé herci](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Vážně zvážit použití pub/sub zasílání zpráv mezi objekty actor pro škálování aplikace. Mezi nástroje, které poskytují tuto službu, patří [opencreate service fabric pub/sub](https://service-fabric-pub-sub.socreate.it/) a [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Vytvořte stav objektu actor jako [podrobný, jak je to možné](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Správa [životního cyklu herce](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Pokud je nebudete znovu používat, odstraňte je. Odstranění nepotřebných objektů actor je obzvláště důležité při použití [zprostředkovatele nestálého stavu](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication), protože veškerý stav je uložen v paměti.
- Z důvodu jejich [tahové souběžnosti](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency), objekty actor se nejlépe používají jako nezávislé objekty. Nevytvářejte grafy více objektových editorů, synchronní volání metod (z nichž každý se s největší pravděpodobností stane samostatným síťovým voláním) ani nevytvářejte kruhové požadavky objektu actor. Ty významně ovlivní výkon a měřítko.
- Nekombinujte synchronizační kód s asynchronním kódem. Používejte asynchronní konzistentně, abyste předešli problémům s výkonem.
- Nevodivé hovory herců. Dlouhotrvající volání bude blokovat další volání stejného objektu actor, z důvodu souběžnosti tahové.
- Pokud komunikujete s jinými službami pomocí [service fabric vzdálené komunikace](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) `ServiceProxyFactory`a vytváříte , vytvořte továrnu na úrovni [actor-service](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) a *nikoli* na úrovni objektu actor.


## <a name="application-diagnostics"></a>Diagnostika aplikací
Buďte důkladní při [přidávání protokolování aplikací](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) do servisních hovorů. To vám pomůže diagnostikovat scénáře, ve kterých služby volat navzájem. Například při volání B c volání D, volání může selhat kdekoli. Pokud nemáte dostatek protokolování, selhání je těžké diagnostikovat. Pokud služby jsou protokolování příliš mnoho z důvodu svazků volání, ujistěte se, že alespoň protokolovat chyby a upozornění.

## <a name="iot-and-messaging-applications"></a>Aplikace IoT a zasílání zpráv
Při čtení zpráv z [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) nebo Azure Event [Hubs](https://docs.microsoft.com/azure/event-hubs/)použijte [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor). ServiceFabricProcessor integruje s Service Fabric spolehlivé služby k udržení stavu čtení z oddílů centra událostí a odesílá nové zprávy do vašich služeb prostřednictvím `IEventProcessor::ProcessEventsAsync()` metody.


## <a name="design-guidance-on-azure"></a>Pokyny k návrhu v Azure
* Navštivte [centrum architektury Azure,](https://docs.microsoft.com/azure/architecture/microservices/) kde najdete pokyny k návrhu [pro vytváření mikroslužeb v Azure](https://docs.microsoft.com/azure/architecture/microservices/).

* Navštivte [stránku Začínáme s Azure for Gaming,](https://docs.microsoft.com/gaming/azure/) kde najdete pokyny k [návrhu používání Service Fabric v herních službách](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).
