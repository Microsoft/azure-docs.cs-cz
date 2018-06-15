---
title: Architektura Resource Manager | Microsoft Docs
description: Přehled architektury portálu Service Fabric clusteru Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 48da92be0eef1154b490fb4829363598d6d66569
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211425"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Přehled architektury správce prostředků clusteru
Správce prostředků clusteru Service Fabric je centrální služba, která běží v clusteru. Spravuje požadovaný stav služeb v clusteru, zejména s ohledem na využití prostředků a pravidla pro umístění. 

Ke správě prostředků v clusteru, musí mít správce prostředků clusteru Service Fabric několik informací:

- Služby, které aktuálně neexistuje.
- Každá služba je aktuální (nebo výchozí) spotřeby prostředků 
- Zbývající kapacita clusteru 
- Počet uzlů v clusteru 
- Množství prostředků na každém uzlu

Časem změnit spotřeby prostředků dané služby a služby obvykle zajímají více než jeden typ prostředku. Mezi různé služby může být skutečně fyzických i fyzické prostředky se měří. Služby mohou sledovat fyzické metriky jako spotřeba paměti a disku. Služby mohou běžně, zajímají logické metriky - věcmi, jako jsou "WorkQueueDepth" nebo "TotalRequests". Logické a fyzické metriky můžete použít ve stejném clusteru. Metriky můžete sdílet mezi mnoha služeb nebo být specifické pro konkrétní službu.

## <a name="other-considerations"></a>Další důležité informace
Vlastníci a operátory clusteru může lišit od autorů služby a aplikace, nebo minimálně jsou stejné vrstvy různých klobouky osoby. Při vývoji aplikace víte, o co vyžaduje pár věcí. Máte odhad bude využívat prostředky a jak nasadit různé služby. Například se webová úroveň je potřeba spustit na uzlech přístup k Internetu, při databázové služby, by měly není. Další příklad webových služeb jsou pravděpodobně omezené procesoru a sítě, při pozor datové vrstvy služby Další informace o spotřebě paměti a disku. Ale uživatel zpracování incident za provozu lokality pro tuto službu v produkci nebo který spravuje upgradu na službu má jinou úlohu chcete provést a vyžaduje různých nástrojů. 

Jsou dynamické clusteru i služby:

- Můžete zvýšit nebo snížit počet uzlů v clusteru
- Může pocházet a přejděte uzly různých velikostí a typů
- Služby mohou být vytvořeny, odebrat a změnit jejich přidělení požadované zdroje a pravidla pro umístění
- Upgrady nebo jiné operace správy, můžete vrátit prostřednictvím clusteru v aplikaci na úrovních infrastruktury
- Selhání může dojít kdykoliv.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Součásti Správce prostředků clusteru a toku dat
Správce prostředků clusteru má trasování požadavky na každé služby a využití prostředků pomocí každého objektu služby v rámci těchto služeb. Správce prostředků clusteru má dvě části koncepční: agentů, kteří běží na každém uzlu a služby odolné proti chybám. Agenti na každý uzel sledování zatížení sestavy ze služeb, agregační je a pravidelně sestavy je. Služba Správce prostředků clusteru agreguje všechny informace ze místní agentů a reaguje na základě jeho aktuální konfigurace.

Podívejme se na následující diagram:

<center>
![Architektura vyrovnávání prostředků][Image1]
</center>

Během modul runtime existuje mnoho změn, které by mohly nastat. Například můžeme vyslovte množství prostředků, které budou využívat některé služby změní, některé služby nezdaří a některé uzly připojení a odpojení clusteru. Všechny změny v uzlu se agregovat a pravidelně odesílají do služby Správce prostředků clusteru (1,2) kde jsou agregovat znovu, analyzovat a uložené. Každých několik sekund, které služba zjistí změny a určuje, zda se všechny akce nezbytné (3). Například může Všimněte si, že byly přidány některé prázdný uzly do clusteru. V důsledku toho rozhodne přesunout některé služby do těchto uzlů. Správce prostředků clusteru může také Všimněte si, že je přetížena konkrétním uzlu nebo že určité služby se nezdařila nebo byla odstraněna, uvolnění prostředků jinde.

Pojďme podívejte se na následující diagram a zjistěte, co se stane dále. Řekněme, že správce prostředků clusteru Určuje, že je nutné provést změny. Ho koordinuje s jinými službami systému (zejména Správce převzetí služeb při selhání) provést potřebné změny. Potřebné příkazy se pak odešlou do příslušné uzly (4). Řekněme například, že správce prostředků si všimli, že počítač Uzel5 byl přetížený a proto se rozhodli pro přesun služby B z počítač Uzel5 do Uzel4. Na konci Rekonfigurace (5) cluster vypadat třeba takto:

<center>
![Architektura vyrovnávání prostředků][Image2]
</center>

## <a name="next-steps"></a>Další postup
- Správce prostředků clusteru má mnoho možností pro popis clusteru. Další informace o nich, projděte si tento článek na [popisující cluster Service Fabric](./service-fabric-cluster-resource-manager-cluster-description.md)
- Primární povinností správce prostředků clusteru jsou vyrovnává clusteru a vynucovat pravidla pro umístění. Další informace o konfiguraci těchto chování najdete v tématu [vyrovnávání cluster Service Fabric](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
