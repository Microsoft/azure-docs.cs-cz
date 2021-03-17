---
title: Architektura Správce prostředků
description: Přehled a architektonické informace o službě Správce prostředků clusteru Azure Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0aff55810508fedcf354fba3d9fca9f7a402029b
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685830"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Přehled architektury cluster Resource Manageru
Cluster Service Fabric Správce prostředků je centrální služba, která běží v clusteru. Spravuje požadovaný stav služeb v clusteru, zejména v souvislosti se spotřebou prostředků a všemi pravidly umístění. 

Pro správu prostředků v clusteru musí mít Service Fabric Správce prostředků clusteru několik informací:

- Které služby aktuálně existují
- Aktuální spotřeba prostředků služby (nebo výchozí). 
- Zbývající kapacita clusteru 
- Kapacita uzlů v clusteru 
- Množství prostředků spotřebovaných v jednotlivých uzlech

Spotřeba prostředků dané služby se může v průběhu času měnit a služby obvykle zajímají více než jeden typ prostředku. U různých služeb se můžou měřit reálné fyzické i fyzické prostředky. Služby mohou sledovat fyzické metriky, jako je paměť a využití disku. Častěji se služby můžou zajímat o logické metriky – například "WorkQueueDepth" nebo "TotalRequests". Ve stejném clusteru se dají použít logické i fyzické metriky. Metriky se dají sdílet napříč celou řadou služeb nebo být specifické pro konkrétní službu.

## <a name="other-considerations"></a>Další důležité informace
Vlastníci a operátoři clusteru se mohou lišit od autorů služeb a aplikací, nebo na minimum jsou stejnými lidmi, kteří využívají různé Hats. Když vyvíjíte aplikaci, znáte pár věcí, o čem to vyžaduje. Máte odhad prostředků, které bude využívat a jakým způsobem by měly být nasazeny různé služby. Například webová vrstva musí být spuštěna na uzlech, které jsou zpřístupněny pro Internet, zatímco databázové služby by neměly. Dalším příkladem je, že webové služby jsou pravděpodobně omezené pomocí procesoru a sítě, zatímco služby datové vrstvy se podrobněji týkají spotřeby paměti a disku. Osoba, která zpracovává živý incident pro tuto službu v produkčním prostředí, nebo která spravuje upgrade na službu, má ale jinou úlohu a vyžaduje jiné nástroje. 

Cluster i služby jsou dynamické:

- Počet uzlů v clusteru se může zvětšovat a zmenšovat.
- Může jít o uzly různých velikostí a typů.
- Služby je možné vytvářet, odebírat a měnit jejich požadovaná přidělení prostředků a pravidla pro umístění.
- Upgrady nebo jiné operace správy mohou clusterovat v aplikaci na úrovních infrastruktury
- K selhání může dojít kdykoli.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Součásti a tok dat cluster Resource Manageru
Cluster Správce prostředků musí sledovat požadavky každé služby a spotřebou prostředků každým objektem služby v těchto službách. Správce prostředků clusteru má dvě koncepční součásti: agenti, kteří se spouštějí na jednotlivých uzlech a službu odolnou proti chybám. Agenti v jednotlivých uzlech sledují sestavy načítání ze služeb, agreguje je a pravidelně hlásí. Služba Správce prostředků clusteru agreguje všechny informace od místních agentů a reaguje na základě její aktuální konfigurace.

Pojďme se podívat na následující diagram:

<center>

![Diagram, který znázorňuje službu Správce prostředků clusteru, agreguje všechny informace z místních agentů a reaguje na základě aktuální konfigurace.][Image1]
</center>

Během běhu je k dispozici mnoho změn, ke kterým může dojít. Řekněme například, že množství prostředků některé služby vybírají změny, některé služby selžou a některé uzly se připojí a opustí cluster. Všechny změny v uzlu jsou agregované a pravidelně se odesílají do clusteru Správce prostředků služby (1, 2), kde se znovu agregují, analyzují a ukládají. Každých pár sekund služba prohlíží změny a určí, jestli jsou potřebné nějaké akce (3). Může se třeba stát, že do clusteru se přidaly nějaké prázdné uzly. V důsledku toho se rozhodne přesunout některé služby do těchto uzlů. Cluster Správce prostředků může také všimnout, že určitý uzel je přetížený nebo že některé služby se nezdařily nebo byly odstraněny, a uvolňuje prostředky jinde.

Pojďme se podívat na následující diagram a podívat se, co se stane dál. Řekněme, že cluster Správce prostředků zjistí, že jsou potřebné změny. Oddělení IT koordinuje jiné systémové služby (zejména Správce převzetí služeb při selhání), aby provedl nezbytné změny. Pak jsou potřebné příkazy odeslány na příslušné uzly (4). Řekněme například, že Správce prostředků všimla, že počítač Uzel5 byl přetížen, a proto se rozhodla přesunout službu B z počítač Uzel5 do Uzel4. Na konci rekonfigurace (5) bude cluster vypadat takto:

<center>

![Architektura nástroje pro vyrovnávání zatížení prostředků][Image2]
</center>

## <a name="next-steps"></a>Další kroky
- Správce prostředků clusteru má mnoho možností pro popis clusteru. Pokud se o nich chcete dozvědět víc, přečtěte si článek [popisující Service Fabric cluster](./service-fabric-cluster-resource-manager-cluster-description.md) .
- Primárními cly Správce prostředků clusteru je vyrovnávání zatížení clusteru a vynucování pravidel umístění. Další informace o konfiguraci tohoto chování najdete v tématu [Balancing a cluster Service Fabric](./service-fabric-cluster-resource-manager-balancing.md) .

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
