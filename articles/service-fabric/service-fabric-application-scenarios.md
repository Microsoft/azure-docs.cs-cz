---
title: Scénáře aplikací a návrh | Microsoft Docs
description: Přehled kategorií cloudové aplikace v Service Fabric. Popisuje návrh aplikace, které používá stavová a Bezstavová services.
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
ms.openlocfilehash: c0a9b24704a91d6a6893937b4ee03765fb05f092
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-application-scenarios"></a>Scénáře aplikací Service Fabric
Azure Service Fabric nabízí spolehlivé a flexibilní platformu, která umožňuje zápis a spustit mnoho typů obchodních aplikací a služeb. Tyto aplikace a mikroslužeb může být bezstavové nebo stavová a jsou vyrovnávání prostředků mezi virtuální počítače chcete maximalizovat efektivitu. Jedinečná architektura Service Fabric umožňuje provádět poblíž analýza dat v reálném čase, výpočtů v paměti, paralelní transakce a událostí zpracování ve svých aplikacích. Je možné snadno škálovat aplikace nahoru nebo dolů (skutečně příchozí nebo odchozí), v závislosti na měnící požadavky na prostředky.

Platforma Service Fabric v Azure je ideální pro následující kategorie aplikace:

* **Vysoce dostupné služby**: služby Service Fabric poskytují rychlý převzetí služeb při selhání tak, že vytvoříte více replik sekundární službu. Pokud uzel, proces nebo jednotlivých služeb přestane fungovat kvůli hardwaru nebo jiné chyby, jednu sekundární repliku je povýšen na primární replice s minimální ztrátě služeb.
* **Škálovatelných služeb**: jednotlivé služby můžete rozdělit na oddíly, aby vám umožnil stav, který má být škálovat napříč clusterem. Kromě toho jednotlivé služby můžete vytvořit a odebrat za chodu. Služby můžete rychle a snadno škálovat na více systémů z několik instancí na několika uzlů tisíce instancí na velký počet uzlů a pak škálovat v znovu, v závislosti na vašich prostředků. Service Fabric slouží k vytvoření těchto služeb a spravovat jejich kompletní životní cykly.
* **Výpočty na nestatické data**: Service Fabric umožňuje sestavit data, vstupu a výstupu a výpočetní náročné stavových aplikací. Service Fabric umožňuje společné umístění zpracování (výpočtů) a data v aplikacích. Za normálních okolností Pokud vaše aplikace vyžaduje přístup k datům, je latence sítě, které jsou spojené s vrstvou externích dat mezipaměti nebo úložiště. S stavové služby Service Fabric je eliminovat této latence, povolení další původce čte a zapisuje. Řekněme například, že máte aplikaci, která provádí téměř v reálném čase doporučení vybrané možnosti pro zákazníky s času jejich návratu požadavek menší než 100 milisekund. Charakteristiky latenci a výkonu služby Service Fabric (kde výpočtu výběru doporučení je umístěn společně s daty a pravidla), poskytuje přizpůsobivý možnosti pro uživatele ve srovnání s modelem standardní implementace toho, že se načíst potřebná data ze vzdáleného úložiště.  
* **Interaktivní aplikace založené na relaci**: Service Fabric je užitečné, pokud vaše aplikace, jako je například online herní nebo zasílání rychlých zpráv a vyžadují, nízkou latencí čtení a zápisy. Service Fabric umožňuje vytvářet tyto interaktivní, stavová aplikace bez nutnosti vytvoření samostatné úložiště nebo mezipaměti, podle potřeby pro bezstavové aplikace. (To zvyšuje latence a potenciálně zavádí konzistence problémy.).
* **Analýza dat a pracovních postupů**: Rychlé čtení a zápisů Service Fabric umožňují aplikacím, které musí spolehlivě zpracovávat události nebo datových proudů. Service Fabric také umožňuje aplikacím, které popisují kanálů pro zpracování, kde musí být výsledky, spolehlivé a předaný do další fáze zpracování bez ztráty. Mezi ně patří transakcí a finanční systémy, kde jsou data konzistence a výpočtů záruky nezbytné.
* **Shromažďování dat, zpracování a IoT**: vzhledem k tomu, že Service Fabric zpracovává velkém měřítku a má nízkou latencí prostřednictvím jeho stavové služby, je ideální pro zpracování dat na miliony zařízení, kde jsou data pro zařízení a výpočet společně umístěné.
Jsme viděli několik zákazníci, kteří mají vestavěné systémy IoT pomocí Service Fabric včetně [BMW](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/24/service-fabric-customer-profile-bmw-technology-corporation/), [Schneider elektrický](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/05/service-fabric-customer-profile-schneider-electric/) a [OK sítě systémy](https://blogs.msdn.microsoft.com/azureservicefabric/2016/06/20/service-fabric-customer-profile-mesh-systems/).

## <a name="application-design-case-studies"></a>Případové studie návrhu aplikace
Počet případové studie znázorňující, jak Service Fabric slouží k návrhu aplikace jsou publikovány na [blog týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/) a [mikroslužeb řešení lokality](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Návrh aplikace skládá z bezstavové a stavové mikroslužeb
Vytváření aplikací u rolí pracovního procesu Azure Cloud Service je příkladem bezstavové služby. Naproti tomu stavová mikroslužeb zachovat jejich autoritativní stavu kromě požadavku a odpovědi. To poskytuje vysokou dostupnost a konzistence stavu prostřednictvím jednoduché rozhraní API, které poskytují transakční záruky zajištěna replikace. Stavové služby Service Fabric demokratizujte vysokou dostupnost, bude pro všechny typy aplikací, nikoli pouze databází a dalších datových úložišť. Toto je přirozené průběh. Aplikací mít již přesunout z pomocí čistě relačních databází pro vysokou dostupnost databáze NoSQL. Nyní aplikace samotné může mít jejich "horkých" stav a data spravovaná v nich pro zvýšení výkonu další, aniž by došlo ke ztrátě spolehlivost, konzistence a dostupnosti.

Při vytváření aplikace, který se skládá z mikroslužeb, obvykle mají kombinaci bezstavové webových aplikací (ASP.NET, Node.js, atd.) volání do firemní bezstavové a stavové služby střední vrstvy, nasazený do stejného clusteru Service Fabric pomocí příkazů nasazení Service Fabric. Každá z těchto služeb je nezávislé s ohledem na využití škálování, spolehlivost a prostředků, výrazně zvýšení flexibility v rámci správy vývoj a životního cyklu.

Stavová mikroslužeb zjednodušit návrhů aplikace, protože odebrat potřebu další fronty a mezipaměti, které tradičně byla požadována dostupnosti a latence požadavky čistě bezstavové aplikace. Vzhledem k tomu, že stavové služby jsou přirozeně vysokou dostupností a s nízkou latencí, to znamená, že jsou méně přesunutí části ke správě v aplikaci jako celek. Následující diagramy znázorňují rozdíly mezi návrhu aplikace, která je bezstavové a ten, který je stavový. Díky [spolehlivé služby](service-fabric-reliable-services-introduction.md) a [Reliable Actors](service-fabric-reliable-actors-introduction.md) programovací modely, stavové služby snížit složitost aplikace při dosažení vysoké prostupnosti a nízké latence.

## <a name="an-application-built-using-stateless-services"></a>Aplikace vyvíjené v bezstavové služby
![Aplikace pomocí bezstavové služby][Image1]

## <a name="an-application-built-using-stateful-services"></a>Aplikace vyvíjené v stavové služby
![Aplikace pomocí bezstavové služby][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup

* Naslouchání na [Zákaznické případové studie](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=qDJnf86yC_5206218965
)
* Přečtěte si informace o [Zákaznické případové studie](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/)
* Další informace o [vzory a scénáře](service-fabric-patterns-and-scenarios.md)

* Začínáme vytváření bezzstavovými i stavovými službami s Service Fabric [spolehlivé služby](service-fabric-reliable-services-quick-start.md) a [spolehlivé aktéři](service-fabric-reliable-actors-get-started.md) programovací modely.
* Také najdete v následujících tématech:
  * [Chci se dozvědět o mikroslužeb](service-fabric-overview-microservices.md)
  * [Definovat a spravovat stav služby](service-fabric-concepts-state.md)
  * [Dostupnost služeb Service Fabric](service-fabric-availability-services.md)
  * [Škálování služby Service Fabric](service-fabric-concepts-scalability.md)
  * [Oddíl služby Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
