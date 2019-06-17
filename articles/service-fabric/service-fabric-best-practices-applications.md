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
ms.date: 04/26/2019
ms.author: msfussell
ms.openlocfilehash: 55f043effc7cdb102acea856e89c58f660d0cde5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65237744"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric application osvědčené postupy pro navrhování

Tento článek obsahuje nejlepších praktik pro vytváření aplikací a služeb v Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Seznamte se s platformou Service Fabric
* [Proto chcete se dozvědět o službě Service Fabric?](service-fabric-content-roadmap.md)
* Přečtěte si informace o [scénáře aplikací Service Fabric](service-fabric-application-scenarios.md)
* Potom pochopit programovací model volby s [Přehled programovacího modelu Service Fabric](service-fabric-choose-framework.md)



## <a name="application-design-guidance"></a>Pokyny k návrhu aplikací
Seznamte se s [obecné architektuře](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) aplikace Service Fabric a jeho [aspekty návrhu](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Zvolit bránu rozhraní API
Použijte službu brány rozhraní API, která komunikuje s back endovým službám, které lze následně škálovat. Nejběžnější služby brány rozhraní API použít, jsou:

- [Azure API Management](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview), což je [integrovaná s platformou Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)
- [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) nebo [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) pomocí [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) ke čtení z oddílů centra událostí
- [Træfik reverzní proxy server](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/) pomocí [zprostředkovatele Azure Service Fabric](https://docs.traefik.io/configuration/backends/servicefabric/)
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/) Poznámka: Toto není přímo integrovaná s platformou Service Fabric a Azure API Management je obvykle upřednostňovanou možnost volby
- Vytvořte svoje vlastní [ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) webové aplikace brány

### <a name="choose-stateless-services"></a>Zvolte bezstavové služby
Doporučujeme vždy spustit sestavování bezstavové služby pomocí [Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) ukládání stavu v databázi Azure, služby Azure cosmos DB nebo úložiště Azure. S stavu externalized je více do hloubky přístup pro většinu vývojářů a umožňuje vám také umožní využívat schopnosti příkazů jazyka ve storu.  

### <a name="when-to-choose-stateful-services"></a>Kdy zvolit stavové služby
Pokud máte scénáře pro zajištění nízké latence a potřebujete uchovávat data blízko výpočetní vezměte v úvahu stavové služby. Mezi příklady patří digitální dvojče zařízení IoT, herní stav, stav relace, ukládání do mezipaměti dat z databáze a dlouho běžící pracovní postupy ke sledování volání do dalších služeb.

Rozhodněte, časový rámec pro uchovávání dat:

- Data uložená v mezipaměti - použijete ukládání do mezipaměti kde čekací doba na externí úložiště se problém. Stavovou službu použijte jako vlastní data ukládat do mezipaměti, nebo zvažte použití [open source SoCreate-fabric distribuované mezipaměť služby](https://github.com/SoCreate/service-fabric-distributed-cache). V tomto scénáři může dojít ke ztrátě všech dat v mezipaměti a nezáleží.
- Časově vázaných dat – je potřeba nechat data blízko sebe vypočítat latence pro určitou dobu, ale tato data můžete dovolit ztratit v *po havárii* scénář. Například v mnoha řešeních IoT, které musí být zavřít výpočetního výkonu, například výpočet průměrnou teplota za posledních několik dní, ale pokud dojde ke ztrátě dat, pak konkrétních datových bodů zaznamenané to důležité nejsou. Také v tomto scénáři vás nezajímají obvykle zálohování jednotlivých datových bodů, pouze počítaný průměrné hodnoty, které jsou zapsány do externího úložiště pravidelně.  
- Dlouhodobá data - Reliable collections mohli data ukládat co trvale. Ale v takovém případě budete muset [Příprava na zotavení po havárii](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery) včetně [konfigurace zásad pravidelné zálohování](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup) vašich clusterů. V důsledku toho nakonfigurujete, co se stane, pokud váš cluster je zničen při scénáři po havárii, kde je potřeba vytvořit nový cluster a jak nasadit nové instance aplikace a obnovit z poslední zálohy.

Úsporu nákladů a zlepšení dostupnosti:
- Můžete snížit náklady s stavové služby, protože nejsou spojené náklady přístup a transakce data ze vzdáleného úložiště a není nutné používat jiné služby, jako je Azure redis Cache.
- Pomocí stavové služby, především pro úložiště a ne za výpočetní výkon je nákladná a nedoporučuje se. Vezměte v úvahu stavové služby jako výpočetní bez levné místní úložiště.
- Odebráním závislosti na dalších službách může zlepšit dostupnost služby. S stav spravovat v clusteru s vysokou DOSTUPNOSTÍ je izoluje z jiné služby výpadky nebo problémy s latencí.

## <a name="how-to-properly-work-with-reliable-services"></a>Jak správně pracovat s modelem Reliable Services
Reliable Services umožňují snadno vytvářet bezstavové a stavové služby. Přečtěte si [seznámení s modelem Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)
- Vždy případném dalším sdílení dodržovat [token zrušení](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps) v `RunAsync()` metodou pro bezstavové a stavové služby a `ChangeRole()` metodu pro stavové služby. Bez toho Service Fabric neví, pokud vaše služba je možné uzavřít. Například není dodržením token rušení, který může vést k mnohem delší doby pro upgrade aplikace.
-   Otevření a zavření [naslouchací procesy komunikace](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication) včas a bude respektovat tokeny zrušení.
-   Nikdy Nekombinujte synchronizovat kódu pomocí asynchronní kód. Například nepoužívejte `.GetAwaiter().GetResult()` v asynchronní volání; musí se jednat o asynchronní *úplně* prostřednictvím zásobníku volání.

## <a name="how-to-properly-work-with-reliable-actors"></a>Jak správně pracovat s Reliable Actors
Reliable Actors umožňuje snadno vytvářet objekty actor stavová a virtuální. Přečtěte si [Úvod do služby Reliable Actors](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)

- Důkladně zvážit možnost použití pub/sub zasílání zpráv mezi vaší actors pro škálování aplikace. Například [open source SoCreate pub/sub](https://service-fabric-pub-sub.socreate.it/) nebo [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).
- Ujistěte se, stavu objektu actor jako [detailní nejrychleji](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices).
- Správa [životního cyklu objektu actor](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices). Odstranění objektů actor Pokud nebudete je někdy znovu použít. To platí zejména při použití [VolatileState poskytovatele](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication) jako všechny stavy jsou uloženy v paměti.
- Z důvodu jejich [souběžnosti založené na řadě vy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency) actors jsou nejvhodnější jako nezávislé objekty. Nemusíte vytvářet grafy více objektů actor, která je synchronní metoda volání (každý z nich pravděpodobně stane samostatnou síť volání) nebo mají cyklické objektu actor požadavků. ty se významně ovlivnit výkon a škálování.
- Nekombinujte synchronizovat kódu pomocí asynchronní kód. musí být zcela asynchronní, aby se zabránilo problémům s výkonem.
- Neprovádějte dlouho běžící volání v objektů actor, zablokují ho pro ostatní volání na stejný objekt actor, z důvodu souběžnosti založená na řadě vy.
- Pokud komunikace s ostatními službami pomocí [vzdálené komunikace Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting) a při vytváření `ServiceProxyFactory`, vytvořte objekt pro vytváření v [služba objektu actor](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using) úroveň a *není*na úrovni objektu actor.


## <a name="application-diagnostics"></a>Konzole Application diagnostics
- Při přidávání důkladné [protokolování aplikací](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) ve volání mezi službami. To pomáhá v diagnostice scénářů, kde služby volají navzájem. Například když -> A B -> C -> D volání by mohlo selhat kdekoli; Pokud není k dispozici dostatek protokolování, je obtížné diagnostikovat. Pokud jsou příliš mnoho z důvodu hovorů, protokolování služby alespoň nezapomeňte protokolovat chyby a upozornění.

## <a name="iot-and-messaging-applications"></a>IoT a aplikace pro zasílání zpráv
Při čtení zprávy z [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) nebo [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) použít [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) , která integruje do Service Fabric Reliable Services udržovat Stav čtení z centra událostí, oddíly a doručí nové zprávy do služby prostřednictvím `IEventProcessor::ProcessEventsAsync()` metody.


## <a name="design-guidance-on-azure"></a>Pokyny k návrhu v Azure
* Přejděte [centrum architektury Azure](https://docs.microsoft.com/azure/architecture/microservices/) pokyny k návrhu [vytváření mikroslužeb v Azure](https://docs.microsoft.com/azure/architecture/microservices/)

* Navštivte [Začínáme s Azure pro hry](https://docs.microsoft.com/gaming/azure/) pokyny k návrhu [pomocí Service Fabric v herních službách](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
