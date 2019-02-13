---
title: Návrh a scénáře aplikací | Dokumentace Microsoftu
description: Přehled kategorií cloudových aplikací ve službě Service Fabric. Tento článek popisuje návrhu aplikace, která používá stavové a bezstavové služby.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/02/2017
ms.author: mfussell
ms.openlocfilehash: a84d8fead42f8314decc144f01d8de1dac30bbdf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199472"
---
# <a name="service-fabric-application-scenarios"></a>Scénáře aplikací Service Fabric
Azure Service Fabric nabízí spolehlivé a flexibilní platformu, která umožňuje zápis a spouštění mnoho typů podnikových aplikací a služeb. Tyto aplikace a mikroslužeb může být Bezstavová nebo stavová a jsou vyrovnávání prostředků napříč virtuálními počítači pro maximalizaci efektivity. Jedinečná architektura Service Fabric umožňuje téměř analýzy dat v reálném čase, výpočty v paměti, paralelní transakce a v aplikacích pro zpracování událostí. Je možné snadno škálovat aplikace navýšení nebo snížení kapacity (ve skutečnosti dovnitř nebo ven), v závislosti na tom se měnící požadavky na prostředky.

Platforma Service Fabric v Azure je ideální pro následující kategorie aplikace:

* **Službami s vysokou dostupností**: Služby Service Fabric poskytují rychlé převzetí služeb při selhání tak, že vytvoříte několik replik sekundární službu. Pokud uzel, procesu nebo jednotlivé služby ocitne mimo provoz kvůli hardwaru nebo jiné chyby, je jednou ze sekundárních replik povýšen na primární replice s minimální ztrátě služeb.
* **Škálovatelných služeb**: Jednotlivé služby můžou být dělené, umožňující stavu škálovat napříč clusterem. Kromě toho jednotlivé služby lze vytvořit a odebírat v reálném čase. Služby můžete rychle a snadno škálovat z několika instancí na několika uzlech pro tisíce instancí na mnoha uzly a potom horizontálně znovu, v závislosti na potřebách prostředků. Service Fabric můžete použít k vytváření těchto služeb a správě jejich kompletní životní cykly.
* **Výpočtu nestatický dat**: Service Fabric umožňuje data sestavení, vstup/výstup a stavové aplikace náročné na výpočetní prostředky. Service Fabric umožňuje kolokaci zpracování (výpočty) a data v aplikacích. Obvykle když vaše aplikace vyžaduje přístup k datům, je latence sítě, které jsou spojené s vrstvou externí data mezipaměti nebo úložiště. S stavové služby Service Fabric je Odstraněná tuto latenci, povolení výkonnější operace čtení a zápisu. Řekněme například, že máte aplikaci, která provádí téměř v reálném čase doporučení výběry pro zákazníky s požadavkem dobu odezvy kratší než 100 milisekund. Latence a výkonové charakteristiky služby Service Fabric (kde výpočet výběru doporučení je umístěna společně s dat a pravidla) poskytuje interaktivní prostředí pro uživatele ve srovnání s model standardní implementace museli načíst potřebná data ze vzdáleného úložiště.  
* **Interaktivní aplikace založené na relaci**: Service Fabric je užitečné, pokud vaše aplikace, jako je například hraní online nebo zasílání rychlých zpráv, vyžadují s nízkou latencí operací čtení a zápisu. Service Fabric umožňuje vytvářet tyto interaktivní a stavových aplikací bez nutnosti vytvářet samostatné úložiště nebo mezipaměť, jak je vyžadováno pro bezstavové aplikace. (To zvyšuje latenci a potenciálně zavádí problémy s konzistencí.).
* **Analýza dat a pracovní postupy**: Rychlé čtení a zápisy Service Fabric umožňují aplikacím, které musí spolehlivě zpracovávat události nebo datových proudů. Service Fabric také umožňuje aplikacím, které popisují kanály zpracování, kde musí být výsledky, spolehlivé a předané do další fáze zpracování bez ztráty. Patří mezi ně transakční a finanční systémy, ve kterém jsou nezbytné záruky konzistence a výpočet data.
* **Shromažďování dat, zpracování a IoT**: Protože Service Fabric zpracovává velkého rozsahu a má nízkou latenci prostřednictvím jeho stavové služby, je ideální pro zpracování dat na miliony zařízení kde jsou umístěné data pro zařízení a výpočtu.
Jsme viděli několik zákazníků, kteří vytvořili IoT systémy s využitím Service Fabric, včetně [BMW](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/24/service-fabric-customer-profile-bmw-technology-corporation/), [Schneider Electric](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/05/service-fabric-customer-profile-schneider-electric/) a [mřížky systémy](https://blogs.msdn.microsoft.com/azureservicefabric/2016/06/20/service-fabric-customer-profile-mesh-systems/).

## <a name="application-design-case-studies"></a>Případové studie návrhu aplikace
Počet případových studií znázorňující, jak Service Fabric se používá při návrhu aplikací, které jsou publikovány na [blog týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/) a [lokality řešení mikroslužeb](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Návrh aplikace skládající se z bezstavových a stavových mikroslužeb
Vytváření aplikací u rolí pracovního procesu Azure Cloud Service je příkladem bezstavovou službu. Naproti tomu stavových mikroslužeb udržovat jejich autoritativní stav i mimo požadavek a odpověď. To poskytuje vysokou dostupnost a konzistence stav prostřednictvím jednoduchých rozhraní API, které představují transakční replikace se opírá o záruku. Stavové služby Service Fabric demokratizujte vysokou dostupnost, přináší na všechny typy aplikací, ne jenom databáze a dalšími datovými úložišti. Toto je přirozeným vývojem. Aplikace už přešli z pomocí čistě relačních databází pro vysokou dostupnost databáze NoSQL. Samotnými aplikacemi teď můžete mít svoje "horkými" stav a data spravovaná v nich pro zvýšení výkonu další, aniž byste museli obětovat spolehlivost, konzistenci a dostupnost.

Při sestavování aplikace skládající se z mikroslužeb, obvykle mají kombinace bezstavových webových aplikací (ASP.NET, Node.js atd.) volání na firemní bezstavové a stavové služby střední vrstvy, nasadit do stejného clusteru Service Fabric pomocí příkazy Service Fabric nasazení. Každá z těchto služeb je nezávislé s ohledem na škálování, spolehlivost a prostředků využití, výrazně zlepšuje flexibilitu ve správě životního cyklu a vývoj.

Stavových mikroslužeb zjednodušit návrhy aplikací, protože odstraňují potřebu další fronty a mezipaměti, které tradičně bylo nezbytné pro splnění požadavků na dostupnost a latenci čistě bezstavových aplikací. Protože stavové služby jsou přirozeně s vysokou dostupností a nízkou latenci, to znamená, že jsou méně pohyblivých částí pro správu ve vaší aplikaci jako celek. Níže uvedené diagramy znázorňují rozdíl mezi návrhu aplikace, která je bezstavové a ten, který je stavový. S využitím [Reliable Services](service-fabric-reliable-services-introduction.md) a [Reliable Actors](service-fabric-reliable-actors-introduction.md) programovací modely, stavové služby zjednodušení aplikací při dosažení vysoké propustnosti a nízké latenci.

## <a name="an-application-built-using-stateless-services"></a>Aplikace vytvořené pomocí bezstavové služby
![Aplikace s použitím bezstavové služby][Image1]

## <a name="an-application-built-using-stateful-services"></a>Aplikace vytvořené pomocí stavové služby
![Aplikace s použitím bezstavové služby][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup

* Přečtěte si informace o [Zákaznické případové studie](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/)
* Další informace o [vzory a scénáře](service-fabric-patterns-and-scenarios.md)

* Začínáme s vytvářením bezstavových a stavových služeb s využitím Service Fabric [modelu reliable services](service-fabric-reliable-services-quick-start.md) a [reliable actors](service-fabric-reliable-actors-get-started.md) programovacích modelů.
* Také naleznete v následujících tématech:
  * [Informace o mikroslužbách](service-fabric-overview-microservices.md)
  * [Definování a správa stavu služby](service-fabric-concepts-state.md)
  * [Dostupnost služeb Service Fabric](service-fabric-availability-services.md)
  * [Škálování služby Service Fabric](service-fabric-concepts-scalability.md)
  * [Segmentace služeb Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
