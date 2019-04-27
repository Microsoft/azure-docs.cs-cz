---
title: Architektura Resource Manageru | Dokumentace Microsoftu
description: Přehled architektury nástroje Service Fabric Cluster Resource Manageru.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bfbdb05e8d2764d2b878e22d236cae30519da176
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62113967"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Přehled architektura cluster resource Manageru
Service Fabric Cluster Resource Manager je centrální služba, která se spustí v clusteru. Spravuje požadovaný stav služby v clusteru, zejména s ohledem na využití prostředků a všechny pravidla pro umístění. 

Ke správě prostředků ve vašem clusteru Service Fabric Cluster Resource Manager musí mít různé druhy informací:

- Služby, které momentálně existují.
- Každá služba je aktuální (nebo výchozí) Spotřeba prostředků 
- Zbývající kapacita clusteru 
- Kapacita uzly v clusteru 
- Množství prostředky spotřebované na každém uzlu

Spotřeba prostředků dané služby můžou časem změnit a služby obvykle záleží více než jeden typ prostředku. Napříč různými službami může být skutečné fyzické i fyzické prostředky, které měří. Služby mohou sledovat fyzické metriky, jako je využití paměti a disku. Služby mohou častěji, záleží logické metriky – věci, jako je "WorkQueueDepth" nebo "TotalRequests". Logické a fyzické metriky je možné ve stejném clusteru. Metriky je možné sdílet napříč řadou služeb nebo být specifické pro konkrétní službu.

## <a name="other-considerations"></a>Další důležité informace
Vlastníci a provozovatelé clusteru může lišit od autorů služby a aplikace, nebo minimálně jsou stejné vrstvy různých hats lidí. Při vývoji aplikace vědět pár věcí o ji vyžaduje. Máte odhad prostředky budou spotřebovávat a toho, jak nasadit různé služby. Například webová vrstva potřebuje ke spuštění na uzlech přístupný z Internetu, zatímco databáze služby by neměla. Další příklad – webové služby jsou pravděpodobně omezeny procesoru a sítě při úroveň dat služby péče o další informace o využití paměti a disku. Však uživatel řešení incidentů živého webu za danou službu v produkčním prostředí nebo kdo spravuje upgrade na službu má různé úlohy provedete a vyžaduje různých nástrojů. 

Clusteru a služeb jsou dynamické vzorce:

- Počet uzlů v clusteru můžete zvětšení a zmenšení
- Uzly z různých velikostech a typech můžete přijít a přejít rovnou
- Služby je možné vytvořit, odebrat a změnit jejich přidělením požadovaných prostředků a pravidla pro umístění
- Upgrady nebo jiné operace správy můžete vrátit do clusteru na úrovni aplikace, na úrovni infrastruktury
- Kdykoli můžete dochází k chybám.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Součásti clusteru resource Manageru a toku dat
Cluster Resource Manager má ke sledování požadavků jednotlivých služeb a spotřebu prostředků každým objektem služby v rámci těchto služeb. Cluster Resource Manager má dvě části koncepční: agenty, které běží na každém uzlu a odolné proti chybám služby. Agenty na každý uzel sledování zatížení sestavy ze služby agregace je a pravidelně dejte nám o nich. Služba Cluster Resource Manageru agreguje všechny Tyhle informace z místní agenti a reaguje na základě své aktuální konfigurace.

Podívejme se na následující diagram:

<center>

![Architektura Resource Balancer][Image1]
</center>

Za běhu existuje mnoho změn, které se může stát. Například můžeme Dejme tomu, že objem prostředků, které využívají některé služby se změní, selhání některých služeb a některé uzly spojení a nechte clusteru. Všechny změny v uzlu se agregují a pravidelně odesílají službě Cluster Resource Manageru (1,2) kde jsou znovu agregují, analyzují a uložené. Každých několik sekund, které služba zjistí změny a určuje, zda všechny akce nezbytné (3). Například může Všimněte si, že byly přidány některé prázdné uzly do clusteru. V důsledku toho se rozhodne přesunout některé služby do těchto uzlů. Cluster Resource Manager může také Všimněte si, že je přetížena konkrétní uzel nebo, aby měli určité služby se nezdařila nebo byla odstraněna, uvolněte prostředky jinde.

Pojďme podívat se na následující diagram a zobrazit, co bude dál. Řekněme, že Cluster Resource Manager určuje, že je nutné provést změny. To koordinuje s dalšími službami systému (zejména Správce převzetí služeb při selhání) provést nezbytné změny. Pak potřebné příkazy jsou odesílány na odpovídající uzly (4). Řekněme například, že správce prostředků si všimli, že Uzel5 přetížená. a proto se rozhodli přesunout služba B v počítači Uzel5 do Uzel4. Na konci (5) konfigurace clusteru vypadá například takto:

<center>

![Architektura Resource Balancer][Image2]
</center>

## <a name="next-steps"></a>Další postup
- Cluster Resource Manager má mnoho možností pro popis clusteru. Další informace o nich najdete v tomto článku na [popisující cluster Service Fabric](./service-fabric-cluster-resource-manager-cluster-description.md)
- Primární povinnosti Cluster Resource Manageru jsou nové vyvážení clusteru a vynucovat pravidla pro umístění. Další informace o konfiguraci těchto projevů najdete v tématu [vyrovnávání vašeho clusteru Service Fabric](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
