---
title: Dostupnost služeb Service Fabric | Microsoft Docs
description: Popisuje zjišťování chyb, převzetí služeb při selhání a obnovení služby
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0794c0e190ecbc4cce808e94f98bb0ac63d1075a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206245"
---
# <a name="availability-of-service-fabric-services"></a>Dostupnost služeb Service Fabric
Tento článek poskytuje přehled o tom, jak Azure Service Fabric udržuje dostupnost služby.

## <a name="availability-of-service-fabric-stateless-services"></a>Dostupnost bezstavové služby Service Fabric
Service Fabric služby mohou být stavová nebo bezstavové. Bezstavové služby je služba aplikace, která nemá [lokálního stavu](service-fabric-concepts-state.md) které musí být vysoce k dispozici nebo není spolehlivá.

Vytvoření bezstavové služby vyžaduje definování `InstanceCount`. Počet instancí definuje počet instancí bezstavové služby aplikační logiky, která by měla být spuštěná v clusteru. Zvýšení počtu instancí je doporučený způsob škálování bezstavové služby.

Pokud instance bezstavové služby s názvem nezdaří, je vytvořena nová instance na oprávněné uzlu v clusteru. Například instance bezstavové služby může dojít k selhání na Uzel1 a znovu vytvořit na počítač Uzel5.

## <a name="availability-of-service-fabric-stateful-services"></a>Dostupnost stavové služby Service Fabric
Stavové služby má stav s ním spojená. V Service Fabric stavové služby je modelovaná jako sadu replik. Každá replika je spuštěné instance kódu služby. Replika taky má kopii stavu pro danou službu. Čtení a zápisu, jsou prováděny operace jednu repliku, volá se *primární*. Změny stavu z zápisu operace jsou *replikovat* do jiných replik sady replik, názvem *aktivní sekundární databáze*a použít. 

Může existovat pouze jedna primární replika, ale může být více aktivní sekundární repliky. Počet aktivní sekundární repliky se dá konfigurovat, a vyšší počet replik tolerovat větší počet souběžných softwaru a selhání hardwaru.

Pokud se primární replika přestane fungovat, Service Fabric provede jeden aktivní sekundární repliky nový primární repliky. Tato aktivní sekundární replika už má v aktualizovaném stavu, prostřednictvím *replikace*, a může pokračovat zpracování další operace čtení a zápisu. Tento proces se označuje jako *Rekonfigurace* a je popsány dále v [Rekonfigurace](service-fabric-concepts-reconfiguration.md) článku.

Koncept se buď primární, nebo aktivní sekundární repliky se označuje jako *role repliky*. Tyto repliky jsou popsány dále v [repliky a instancí](service-fabric-concepts-replica-lifecycle.md) článku. 

## <a name="next-steps"></a>Další postup
Další informace o konceptech Service Fabric najdete v následujících článcích:

- [Škálování služby Service Fabric](service-fabric-concepts-scalability.md)
- [Vytváření oddílů služby Service Fabric](service-fabric-concepts-partitioning.md)
- [Definování a správu stavu](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

