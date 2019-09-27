---
title: Scénáře a návrh aplikací | Microsoft Docs
description: Přehled kategorií cloudových aplikací v Service Fabric. Popisuje návrh aplikace, který používá stavové a bezstavové služby.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/24/2019
ms.author: atsenthi
ms.openlocfilehash: 91e85f762e05c836fe32f5743cc48afed30ae983
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327294"
---
# <a name="service-fabric-application-scenarios"></a>Service Fabric scénáře aplikací
Azure Service Fabric nabízí spolehlivou a flexibilní platformu, kde můžete psát a spouštět mnoho typů obchodních aplikací a služeb. Tyto aplikace a mikroslužby můžou být bezstavové nebo stavové a jsou mezi virtuálními počítači vyrovnávány prostředky, aby se maximalizovala efektivita. 

Jedinečná architektura Service Fabric umožňuje provádět analýzu dat v reálném čase, výpočet v paměti, paralelní transakce a zpracování událostí ve vašich aplikacích. V závislosti na změně požadavků na prostředky můžete své aplikace snadno škálovat směrem nahoru nebo dolů (ve skutečnosti nebo z výstupů).

Pokyny k návrhu při sestavování aplikací, čtení [architektury mikroslužeb v Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) a [osvědčených postupech pro návrh aplikace pomocí Service Fabric](service-fabric-best-practices-applications.md).

Zvažte použití platformy Service Fabric pro následující typy aplikací:

* **Shromažďování dat, zpracování a IoT**: Service Fabric zpracovává velký rozsah a má nízkou latenci prostřednictvím stavových služeb. Může napomáhat zpracování dat v milionech zařízení, kde se společně nacházejí data pro zařízení a výpočty.

    Zákazníci, kteří sestavili služby IoT pomocí Service Fabric, zahrnují [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [vytváření PCL](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [Schneider elektrických](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)a [mřížkové systémy](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Hraní her a interaktivní aplikace založené na relacích**: Service Fabric je užitečné, pokud vaše aplikace vyžaduje čtení a zápis s nízkou latencí, například v online herním nebo rychlém zasílání zpráv. Service Fabric vám umožní vytvářet tyto interaktivní a stavové aplikace, aniž byste museli vytvářet samostatné úložiště nebo mezipaměť. Pokyny k [používání Service Fabric v herních službách](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)najdete v [řešeních her v Azure](https://azure.microsoft.com/solutions/gaming/) .

    Mezi zákazníky, kteří vytvořili herní služby, patří [Další hry](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) a [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Zákazníci, kteří vytvořili interaktivní relace, zahrnují [Honeywell s HoloLens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Analýza dat a zpracování pracovního postupu**: Aplikace, které musí spolehlivě zpracovávat události nebo datové proudy s využitím optimalizovaného čtení a zápisu v Service Fabric. Service Fabric podporuje také kanály zpracování aplikací, kde musí být výsledky spolehlivé a předávané do další fáze zpracování bez jakýchkoli ztrát. Tyto kanály zahrnují transakční a finanční systémy, kde jsou zásadní záruky konzistence a výpočtu dat.

    Zákazníci, kteří mají sestavené služby podnikového pracovního postupu, zahrnují [skupinu Zeiss](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [řešení pro kvorum](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)a [obecnou Société](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Výpočet dat**: Service Fabric vám umožní sestavovat stavové aplikace, které mají náročné výpočty dat. Service Fabric umožňuje společné umístění zpracování (výpočtu) a dat v aplikacích. 

   Když vaše aplikace obvykle vyžaduje přístup k datům, latence sítě přidružená k externí mezipaměti dat nebo vrstvě úložiště omezuje dobu výpočtu. Stavové Service Fabric služby eliminují tuto latenci a povolují více optimalizovaných čtení a zápisů. 
   
   Představte si například aplikaci, která provádí výběr doporučení téměř v reálném čase pro zákazníky s požadavkem na dobu odezvy menší než 100 milisekund. Vlastnosti latence a výkonu služby Service Fabric Services poskytují uživateli odpovídající možnosti, které jsou porovnány s modelem implementace Standard, který potřebuje načíst potřebná data ze vzdáleného úložiště. Systém je rychlejší, protože výpočet výběru doporučení se nachází společně s daty a pravidly.

    Zákazníci, kteří vytvořili výpočetní služby, zahrnují [odpověď Solidsoft](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) a [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Služby s vysokou dostupností**: Service Fabric poskytuje rychlé převzetí služeb při selhání vytvořením několika replik sekundární služby. Pokud dojde k výpadku uzlu, procesu nebo samostatné služby z důvodu hardwaru nebo jiné chyby, bude jedna ze sekundárních replik povýšena na primární repliku s minimální ztrátou služby.

* **Škálovatelné služby**: Jednotlivé služby je možné rozdělit na oddíly a umožnit tak horizontální navýšení kapacity napříč clusterem. Jednotlivé služby je také možné vytvořit a odebrat. Můžete škálovat služby z několika instancí na několika uzlech na tisíce instancí na mnoha uzlech a pak je znovu škálovat podle potřeby. Pomocí Service Fabric můžete tyto služby vytvářet a spravovat jejich kompletní životní cykly.

## <a name="application-design-case-studies"></a>Případové studie pro návrh aplikace
Případové studie, které ukazují, jak Service Fabric slouží k návrhu aplikací, jsou publikovány v [zákaznických scénářích](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) a [mikroslužbách](https://azure.microsoft.com/solutions/microservice-applications/) na webech Azure.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Navrhování aplikací složených z bezstavových a stavových mikroslužeb
Příkladem stavové služby je sestavování aplikací pomocí rolí pracovního procesu Azure Cloud Services. Naproti tomu stavové mikroslužby udržují svůj autoritativní stav mimo požadavek a odpověď. Tato funkce poskytuje vysokou dostupnost a konzistenci stavu prostřednictvím jednoduchých rozhraní API, která poskytují transakční záruky zálohované replikací. 

Stavové služby v Service Fabric přinesou vysokou dostupnost pro všechny typy aplikací, nikoli jenom databáze a další úložiště dat. Jedná se o přirozený pokrok. Aplikace se už přesunuly z použití čistě relačních databází pro zajištění vysoké dostupnosti pro databáze NoSQL. Samotné aplikace teď můžou mít svůj "horkou" stav a data spravovaná v nich, aby se daly zvýšit výkon, aniž by došlo ke ztrátě spolehlivosti, konzistence nebo dostupnosti.

Při sestavování aplikací, které se skládají z mikroslužeb, obvykle máte k dispozici kombinaci bezstavových webových aplikací (například ASP.NET a Node. js), která zavolá bezstavové a stavové služby firmy střední úrovně. Aplikace a služby jsou nasazené ve stejném Service Fabric clusteru prostřednictvím příkazů Service Fabric nasazení. Každá z těchto služeb je nezávislá na použití škálování, spolehlivosti a využití prostředků. Tato nezávislost vylepšuje flexibilitu a flexibilitu při vývoji a správě životního cyklu.

Stavové mikroslužby zjednodušují návrhy aplikací, protože odstraňují nutnost dalších front a mezipamětí, které byly tradičně nutné pro řešení požadavků na dostupnost a latenci čistě bezstavových aplikací. Vzhledem k tomu, že stavová služba má vysokou dostupnost a nízkou latenci, je pro správu ve vaší aplikaci méně podrobností. 

Následující diagramy znázorňují rozdíly mezi návrhem aplikace, která je Bezstavová a ta je stavová. Díky využití [Reliable Services](service-fabric-reliable-services-introduction.md) a [Reliable Actors](service-fabric-reliable-actors-introduction.md) programovacích modelů omezuje stavové služby složitost aplikace při dosahování vysoké propustnosti a nízké latence.

Tady je ukázková aplikace, která používá bezstavové služby: @no__t – 0Application používající bezstavové služby @ no__t-1

Tady je ukázková aplikace, která používá stavové služby: @no__t – 0Application používající bezstavové služby @ no__t-1

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [vzorcích a scénářích](service-fabric-patterns-and-scenarios.md).

* Začněte vytvářet bezstavové a stavové služby pomocí Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) a [Reliable Actors](service-fabric-reliable-actors-get-started.md) programovacích modelů.
* Pokyny k [vytváření mikroslužeb v Azure](https://docs.microsoft.com/azure/architecture/microservices/)najdete na cetrum architektury Azure.
* Přejít na [Azure Service Fabric a osvědčené postupy](service-fabric-best-practices-overview.md) pro vytváření aplikací pro návrh aplikace.

* Podívejte se také na tato témata:
  * [Informace o mikroslužbách](service-fabric-overview-microservices.md)
  * [Definování a Správa stavu služby](service-fabric-concepts-state.md)
  * [Dostupnost služeb Service Fabric Services](service-fabric-availability-services.md)
  * [Škálování služby Service Fabric Services](service-fabric-concepts-scalability.md)
  * [Service Fabric služby oddílů](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
