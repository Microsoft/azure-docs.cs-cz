---
title: Azure Service Fabric application osvědčené postupy pro navrhování | Dokumentace Microsoftu
description: Osvědčené postupy pro vývoj aplikací Service Fabric.
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: msfussell
ms.openlocfilehash: 30d696337061ade6b79c7ec0e4c4de67651f0dad
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203459"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric application osvědčené postupy pro navrhování

Tento článek obsahuje nejlepších praktik pro vytváření aplikací a služeb na Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Seznamte se s platformou Service Fabric
* Přečtěte si [tak, že chcete další informace o Service Fabric?](service-fabric-content-roadmap.md) článku.
* Přečtěte si informace o [scénáře aplikací Service Fabric](service-fabric-application-scenarios.md).
* Vysvětlení programovací model volby načtením [Přehled programovacího modelu Service Fabric](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Pokyny k návrhu aplikací
Seznamte se s [obecné architektuře](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) aplikací Service Fabric a jejich [aspekty návrhu](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Zvolit bránu rozhraní API
Použijte službu brány rozhraní API, která komunikuje s back endovým službám, které lze následně škálovat. Nejběžnější služby brány rozhraní API použít, jsou:

- [Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), což je [integrovaná s platformou Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management).
- [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) nebo [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/), použije [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) ke čtení z oddílů centra událostí.
- [Træfik reverzní proxy server](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/), použije [zprostředkovatele Azure Service Fabric](https://docs.traefik.io/configuration/backends/servicefabric/).
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/).

   > [!NOTE] 
   > Azure Application Gateway není přímo integrovaná s platformou Service Fabric. Azure API Management je obvykle upřednostňovanou možnost.
- Vlastní míru sestavené [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) gateway webových aplikací.

### <a name="stateless-services"></a>Bezstavové služby
Doporučujeme, aby vždy začněte vytvářet bezstavové služby pomocí [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) a ukládání stavu v databázi Azure, Azure Cosmos DB nebo úložiště Azure. Externalized stavu je více do hloubky přístup pro většinu vývojářů. Tento přístup také umožňuje využívat schopnosti příkazů jazyka ve storu.  

### <a name="when-to-use-stateful-services"></a>Kdy použít stavové služby
Pokud máte scénáře pro zajištění nízké latence a potřebujete uchovávat data blízko výpočetní vezměte v úvahu stavové služby. Ukázkové scénáře zahrnují digitální dvojče zařízení IoT, herní stav, stav relace, ukládání dat z databáze a dlouhotrvající pracovní postupy ke sledování volání do dalších služeb.

Při rozhodování o časový rámec pro uchovávání dat:

- **Data v mezipaměti**. Použití ukládání do mezipaměti při čekací doba na externí úložiště se problém. Stavovou službu použijte jako mezipaměť dat, nebo zvažte použití [open source SoCreate Service Fabric distribuované mezipaměti](https://github.com/SoCreate/service-fabric-distributed-cache). V tomto scénáři nemusíte mít obavy pokud ztratíte všechna data v mezipaměti.
- **Časově vázaných dat**. V tomto scénáři je potřeba nechat data blízko sebe vypočítat pro určitou dobu latence, ale můžete si může dovolit přijít o data v *po havárii*. V mnoha řešeních IoT, například data musí být zavřít výpočetní prostředky, například když se počítá průměrná teplota za posledních několik dnů, ale pokud tato data se ztratí, zaznamenávají konkrétní datové body nejsou to důležité. Také v tomto scénáři nezáleží obvykle zálohování jednotlivých datových bodů. Můžete zálohovat pouze počítaný průměrné hodnoty, které pravidelně zapíšou do externího úložiště.  
- **Dlouhodobá data**. Spolehlivé kolekce můžete ukládat data trvale. V tomto případě je potřeba, ale [Příprava na zotavení po havárii](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery), včetně [konfigurace zásad pravidelné zálohování](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) vašich clusterů. V důsledku toho nakonfigurujete, co se stane, pokud váš cluster je zničen při katastrofě, kde je potřeba vytvořit nový cluster a jak nasadit nové instance aplikace a obnovit z poslední zálohy.

Úsporu nákladů a zlepšení dostupnosti:
- Můžete snížit náklady s využitím stavové služby, protože se vám neúčtovaly přístup k datům a náklady na transakce ze vzdáleného úložiště a protože není nutné používat jiné služby, jako je Azure Cache pro redis Cache.
- Pomocí stavové služby, především pro úložiště a ne za výpočetní výkon je nákladné a to nedoporučujeme. Stavové služby si můžete představit jako výpočetní bez levné místní úložiště.
- Odebráním závislosti na dalších službách může zlepšit dostupnost služby. Správa stavu s vysokou DOSTUPNOSTÍ v clusteru je izoluje od jiných služeb výpadky nebo problémy s latencí.

## <a name="how-to-work-with-reliable-services"></a>Práce s Reliable Services
Service Fabric Reliable Services umožňuje snadno vytvářet bezstavové a stavové služby. Další informace najdete v tématu [seznámení s modelem Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction).
- Vždy případném dalším sdílení dodržovat [token zrušení](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) v `RunAsync()` metodou pro bezstavové a stavové služby a `ChangeRole()` metodu pro stavové služby. Pokud to neuděláte, Service Fabric nebude vědět, pokud vaše služba je možné uzavřít. Například pokud není dodržena token zrušení, může dojít mnohem delší doby pro upgrade aplikace.
-   Otevření a zavření [naslouchací procesy komunikace](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) včas a bude respektovat tokeny zrušení.
-   Nikdy Nekombinujte synchronizovat kódu pomocí asynchronní kód. Například nepoužívejte `.GetAwaiter().GetResult()` v asynchronní volání. Použít operátory async *úplně* prostřednictvím zásobníku volání.

## <a name="how-to-work-with-reliable-actors"></a>Práce s Reliable Actors
Service Fabric Reliable Actors umožňuje snadno vytvářet objekty actor stavová a virtuální. Další informace najdete v tématu [Úvod do služby Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction).

- Vážně zvažte použití pub/sub zasílání zpráv mezi vaší actors pro škálování vaší aplikace. Zahrnují nástroje, které tuto službu poskytovat [open source SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/) a [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Ujistěte se, stavu objektu actor jako [detailní nejrychleji](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Správa [životního cyklu objektu actor](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Odstranění objektů actor Pokud nebudete ji použijí znovu. Odstranění nepotřebných actors je zvlášť důležité při použití [zprostředkovatel volatile stavu](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication), protože všechny stavy jsou uloženy v paměti.
- Z důvodu jejich [souběžnosti založená na řadě vy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency), actors jsou nejvhodnější jako nezávislé objekty. Nevytváří grafy více objektů actor, která je synchronní metoda volání (každý z nich pravděpodobně stane samostatnou síť volání) ani vytváření žádostí o cyklické objektu actor. Ty se významně ovlivnit výkon a škálování.
- Nekombinujte synchronizovat kódu pomocí asynchronní kód. Používejte operátory async konzistentně, aby se zabránilo problémům s výkonem.
- Nenastavujte dlouhého volání v actors. Dlouhého volání bude blokovat jiných volání na stejný objekt actor, z důvodu souběžnosti založená na řadě vy.
- Pokud jste komunikaci s dalšími službami pomocí [vzdálené komunikace Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) a vytváříte `ServiceProxyFactory`, vytváření na [služba objektu actor](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) úroveň a *není* na úrovni objektu actor.


## <a name="application-diagnostics"></a>Konzole Application diagnostics
Důkladné o přidání [protokolování aplikací](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) ve volání mezi službami. To vám pomůže diagnostikovat scénáře, ve kterých služby volají navzájem. Například při volání volání volá B C D, volání můžou selhat kdekoli. Pokud nemáte dostatek protokolování, je těžké diagnostikovat selhání. Pokud služby předplatitelů příliš mnoho z důvodu objemy volání, ujistěte se, že jste nejméně protokolovat chyby a upozornění.

## <a name="iot-and-messaging-applications"></a>IoT a aplikace pro zasílání zpráv
Když při čtení zprávy z [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) nebo [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/), použijte [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor). ServiceFabricProcessor integruje do Service Fabric Reliable Services k zachování stavu čtení z centra událostí, oddíly a doručí nové zprávy do služby prostřednictvím `IEventProcessor::ProcessEventsAsync()` metody.


## <a name="design-guidance-on-azure"></a>Pokyny k návrhu v Azure
* Přejděte [centrum architektury Azure](https://docs.microsoft.com/azure/architecture/microservices/) pokyny k návrhu [vytváření mikroslužeb v Azure](https://docs.microsoft.com/azure/architecture/microservices/).

* Navštivte [Začínáme s Azure pro hry](https://docs.microsoft.com/gaming/azure/) pokyny k návrhu [pomocí Service Fabric v herních službách](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).
