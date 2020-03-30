---
title: Architektura Správce prostředků
description: Přehled a architektonické informace o službě Azure Service Fabric Cluster Resource Manager.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 94ed906533d108081d620e9b183ecfee249d85ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551688"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Přehled architektury správce prostředků clusteru
Správce prostředků clusteru Service Fabric je centrální služba spuštěná v clusteru. Spravuje požadovaný stav služeb v clusteru, zejména s ohledem na spotřebu prostředků a všechna pravidla umístění. 

Chcete-li spravovat prostředky v clusteru, správce prostředků clusteru Service Fabric musí mít několik informací:

- Které služby v současné době existují
- Aktuální (nebo výchozí) spotřeba zdrojů každé služby 
- Zbývající kapacita clusteru 
- Kapacita uzlů v clusteru 
- Množství prostředků spotřebovaných v každém uzlu

Spotřeba prostředků dané služby se může v průběhu času měnit a služby se obvykle starají o více než jeden typ prostředku. V různých službách se mohou měřit skutečné fyzické i fyzické zdroje. Služby mohou sledovat fyzické metriky, jako je spotřeba paměti a disku. Více obyčejně služby může starat o logické metriky – věci jako "WorkQueueDepth" nebo "TotalRequests". Logické i fyzické metriky lze použít ve stejném clusteru. Metriky lze sdílet v mnoha službách nebo být specifické pro konkrétní službu.

## <a name="other-considerations"></a>Další aspekty
Vlastníci a provozovatelé clusteru se mohou lišit od autorů služeb a aplikací, nebo minimálně jsou stejní lidé, kteří nosí různé klobouky. Při vývoji aplikace víte několik věcí o tom, co to vyžaduje. Máte odhad prostředků, které bude spotřebovávat a jak by měly být nasazeny různé služby. Například webová vrstva musí být spuštěna na uzlech vystavených Internetu, zatímco databázové služby by neměly. Jako další příklad jsou webové služby pravděpodobně omezeny procesorem a sítí, zatímco služby datové vrstvy se více starají o spotřebu paměti a disku. Osoba, která zpracovává incident živého webu pro tuto službu v produkčním prostředí nebo která spravuje upgrade na službu, však má jinou úlohu a vyžaduje různé nástroje. 

Cluster i služby jsou dynamické:

- Počet uzlů v clusteru může růst a zmenšovat
- Uzly různých velikostí a typů mohou přicházet a odcházet
- Služby lze vytvářet, odebírat a měnit požadovaná přidělení zdrojů a pravidla umístění.
- Upgrady nebo jiné operace správy se můžou procházet clusterem v aplikaci na úrovních infrastruktury.
- K chybám může dojít kdykoli.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Součásti správce prostředků clusteru a tok dat
Správce prostředků clusteru musí sledovat požadavky jednotlivých služeb a spotřebu prostředků jednotlivými objekty služby v rámci těchto služeb. Správce prostředků clusteru má dvě koncepční části: agenty, které běží na každém uzlu a službu odolnou proti chybám. Agenti v každém uzlu sledovat sestavy zatížení ze služeb, agregovat je a pravidelně hlásit. Služba Správce prostředků clusteru agreguje všechny informace od místních agentů a reaguje na základě aktuální konfigurace.

Podívejme se na následující diagram:

<center>

![Architektura balanceru prostředků][Image1]
</center>

Během běhu existuje mnoho změn, které by se mohly stát. Řekněme například, že množství prostředků, které některé služby spotřebovávají, některé služby se nezdaří a některé uzly se připojují a opouštějí cluster. Všechny změny v uzlu jsou agregovány a pravidelně odesílány do služby Správce prostředků clusteru (1,2), kde jsou znovu agregovány, analyzovány a uloženy. Každých několik sekund, že služba se dívá na změny a určuje, zda jsou nutné nějaké akce (3). Může například zaznamenat, že některé prázdné uzly byly přidány do clusteru. V důsledku toho se rozhodne přesunout některé služby do těchto uzlů. Správce prostředků clusteru si také může všimnout, že určitý uzel je přetížený nebo že některé služby selhaly nebo byly odstraněny, což uvolňuje prostředky jinde.

Podívejme se na následující diagram a uvidíme, co se stane dál. Řekněme, že Správce prostředků clusteru určuje, že změny jsou nezbytné. Koordinuje s ostatními systémovými službami (zejména správce mašit failover) provést nezbytné změny. Poté jsou potřebné příkazy odeslány do příslušných uzlů (4). Řekněme například, že Správce prostředků si všiml, že Node5 byl přetížen, a proto se rozhodl přesunout službu B z Node5 na Node4. Na konci rekonfigurace (5) vypadá cluster takto:

<center>

![Architektura balanceru prostředků][Image2]
</center>

## <a name="next-steps"></a>Další kroky
- Správce prostředků clusteru má mnoho možností pro popis clusteru. Další informace o nich naleznete v tomto článku [popisujícím cluster Service Fabric](./service-fabric-cluster-resource-manager-cluster-description.md)
- Primárními povinnostmi Správce prostředků clusteru jsou vyvažování clusteru a vynucování pravidel umístění. Další informace o konfiguraci tohoto chování naleznete v [tématu vyrovnávání clusteru Service Fabric](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
