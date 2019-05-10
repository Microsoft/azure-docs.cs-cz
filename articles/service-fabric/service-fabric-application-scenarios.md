---
title: Návrh a scénáře aplikací | Dokumentace Microsoftu
description: Přehled kategorií cloudových aplikací ve službě Service Fabric. Tento článek popisuje návrhu aplikace, která používá stavové a bezstavové služby.
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
ms.openlocfilehash: f7ad295eb30d257cd195ae02d5a5b1d85de76bda
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228496"
---
# <a name="service-fabric-application-scenarios"></a>Scénáře aplikací Service Fabric
Azure Service Fabric nabízí spolehlivé a flexibilní platformu, která umožňuje zápis a spouštění mnoho typů podnikových aplikací a služeb. Tyto aplikace a mikroslužeb může být Bezstavová nebo stavová a jsou vyrovnávání prostředků napříč virtuálními počítači pro maximalizaci efektivity. Jedinečná architektura Service Fabric umožňuje téměř analýzy dat v reálném čase, výpočty v paměti, paralelní transakce a v aplikacích pro zpracování událostí. Je možné snadno škálovat aplikace navýšení nebo snížení kapacity (ve skutečnosti dovnitř nebo ven), v závislosti na tom se měnící požadavky na prostředky.

Pokyny k návrhu na vytváření aplikací, najdete v článku [architekturu mikroslužeb v Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) a [osvědčené postupy pro navrhování aplikací pomocí Service Fabric](service-fabric-best-practices-applications.md)

Platforma Service Fabric je ideální pro následující typy aplikací:

* **Shromažďování dat, zpracování a IoT**: Protože Service Fabric zpracovává velkého rozsahu a má nízkou latenci prostřednictvím jeho stavové služby, je ideální pro zpracování dat na miliony zařízení kde jsou společně umísťovat data pro zařízení a výpočtu.

    Zákazníci, kteří vytvořili služby IoT pomocí Service Fabric zahrnují [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [ Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric), a [mřížky systémy](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Hraní her a na základě relace interaktivních aplikací**: Service Fabric je ideální, pokud vaše aplikace vyžaduje s nízkou latencí operací čtení a zápisu, třeba v online hrách nebo zasílání rychlých zpráv. Service Fabric umožňuje vytvářet tyto interaktivní a stavových aplikací bez nutnosti vytvářet samostatné úložiště nebo mezipaměť. Navštivte [Azure herních řešení](https://azure.microsoft.com/solutions/gaming/) pokyny k návrhu [pomocí Service Fabric v herních službách](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    Zákazníci, kteří vytvořili herní služby zahrnují [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) a [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Zákazníci, kteří vytvořili interaktivní relace zahrnují [Honeywell s Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **Analýza dat a zpracování pracovního postupu**: Aplikace, které musí spolehlivě zpracovávat události nebo datových proudů také výhody optimalizované operací čtení a zápisů v Service Fabric. Navíc Service Fabric podporuje kanály zpracování aplikací, kde musí být výsledky, spolehlivá a předané do další fáze zpracování beze ztrát. Patří mezi ně transakční a finanční systémy, ve kterém jsou nezbytné záruky konzistence a výpočet data.

    Zákazníci, kteří vytvořili firemní služby pracovního postupu zahrnout [Zeiss skupiny](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform) a [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)

* **Výpočet dat**: Service Fabric umožňuje vytvářet datové výpočty náročné na stavové aplikace. Service Fabric umožňuje společné umístění zpracování (výpočty) a data v aplikacích. Za normálních okolností když vaše aplikace vyžaduje přístup k datům, latence sítě, které jsou spojené s vrstvou mezipaměti nebo úložiště externích dat omezuje dobu výpočtu. Stavové služby Service Fabric se eliminují, že latence, povolení více optimalizované čte a zapisuje. Zvažte například aplikaci, která provádí téměř v reálném čase doporučení výběry pro zákazníky s požadavkem dobu odezvy kratší než 100 milisekund. Latence a výkonové charakteristiky služby Service Fabric (kde výpočet výběru doporučení je umístěna společně s daty a pravidla) poskytuje interaktivní prostředí pro uživatele ve srovnání s model standardní implementace museli načíst potřebná data ze vzdáleného úložiště.

    Zákazníci, kteří vytvořili výpočetní služby zahrnují [Solidsoft odpověď](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) a [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)

* **Službami s vysokou dostupností**: Service Fabric nabízí rychlé převzetí služeb při selhání tak, že vytvoříte několik replik sekundární službu. Pokud uzel, procesu nebo jednotlivé služby ocitne mimo provoz kvůli hardwaru nebo jiné chyby, je jednou ze sekundárních replik povýšen na primární replice s minimální ztrátě služeb.

* **Škálovatelných služeb**: Jednotlivé služby můžou být dělené, umožňující stavu škálovat napříč clusterem. Kromě toho jednotlivé služby lze vytvořit a odebírat v reálném čase. Služby mohou být rychle a snadno škálovat z několika instancí na několika uzlech pro tisíce instancí na mnoha uzly a potom horizontálně znovu, v závislosti na potřebách prostředků. Service Fabric můžete použít k vytváření těchto služeb a správě jejich kompletní životní cykly.

## <a name="application-design-case-studies"></a>Případové studie návrhu aplikace
Počet případových studií znázorňující, jak Service Fabric se používá při návrhu aplikací, které jsou publikovány v [příběhy zákazníků](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/) a [lokality řešení mikroslužeb](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Návrh aplikace skládající se z bezstavových a stavových mikroslužeb
Vytváření aplikací u rolí pracovního procesu Azure Cloud Service je příkladem bezstavovou službu. Naproti tomu stavových mikroslužeb udržovat jejich autoritativní stav i mimo požadavek a odpověď. Tato funkce poskytuje vysokou dostupnost a konzistence stav prostřednictvím jednoduchých rozhraní API, které představují transakční replikace se opírá o záruku. Stavové služby Service Fabric demokratizujte vysokou dostupnost, přináší na všechny typy aplikací, ne jenom databáze a dalšími datovými úložišti. Toto je přirozeným vývojem. Aplikace už přešli z pomocí čistě relačních databází pro vysokou dostupnost databáze NoSQL. Samotnými aplikacemi teď můžete mít svoje "horkými" stav a data spravovaná v nich pro zvýšení výkonu další, aniž byste museli obětovat spolehlivost, konzistenci a dostupnost.

Při sestavování aplikace skládající se z mikroslužeb, obvykle mají kombinace bezstavových webových aplikací (ASP.NET, Node.js atd.) volání na firemní bezstavové a stavové služby střední vrstvy, nasadit do stejného clusteru Service Fabric pomocí příkazy Service Fabric nasazení. Každá z těchto služeb je nezávislé s ohledem na škálování, spolehlivost a využití prostředků, výrazně zlepšit agilnost a flexibilitu při vývoji a správě životního cyklu.

Stavových mikroslužeb zjednodušit návrhy aplikací, protože odstraňují potřebu další fronty a mezipaměti, které tradičně bylo nezbytné pro splnění požadavků na dostupnost a latenci čistě bezstavových aplikací. Protože stavové služby přirozeně mít vysokou dostupnost a nízkou latenci, jsou méně pohyblivých částí pro správu ve vaší aplikaci jako celek. Níže uvedené diagramy znázorňují rozdíl mezi návrhu aplikace, která je bezstavové a ten, který je stavový. S využitím [Reliable Services](service-fabric-reliable-services-introduction.md) a [Reliable Actors](service-fabric-reliable-actors-introduction.md) programovací modely, stavové služby zjednodušení aplikací při dosažení vysoké propustnosti a nízké latenci.

## <a name="an-application-built-using-stateless-services"></a>Aplikace vytvořené pomocí bezstavové služby
![Aplikace s použitím bezstavové služby][Image1]

## <a name="an-application-built-using-stateful-services"></a>Aplikace vytvořené pomocí stavové služby
![Aplikace s použitím bezstavové služby][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup

* Přečtěte si informace o [Zákaznické případové studie](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc)
* Další informace o [vzory a scénáře](service-fabric-patterns-and-scenarios.md)

* Začínáme s vytvářením bezstavových a stavových služeb s využitím Service Fabric [modelu reliable services](service-fabric-reliable-services-quick-start.md) a [reliable actors](service-fabric-reliable-actors-get-started.md) programovacích modelů.
* Navštivte centrum architektury Azure pokyny na [vytváření mikroslužeb v Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* Přejděte na [cluster a aplikace Azure Service Fabric osvědčené postupy](service-fabric-best-practices-overview.md) pokyny k návrhu aplikací.

* Také naleznete v následujících tématech:
  * [Informace o mikroslužbách](service-fabric-overview-microservices.md)
  * [Definování a správa stavu služby](service-fabric-concepts-state.md)
  * [Dostupnost služeb Service Fabric](service-fabric-availability-services.md)
  * [Škálování služby Service Fabric](service-fabric-concepts-scalability.md)
  * [Segmentace služeb Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
