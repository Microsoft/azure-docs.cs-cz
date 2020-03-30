---
title: Dostupnost služeb Service Fabric
description: Popisuje zjišťování chyb, převzetí služeb při selhání a obnovení služby v aplikaci Azure Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551858"
---
# <a name="availability-of-service-fabric-services"></a>Dostupnost služeb Service Fabric
Tento článek poskytuje přehled o tom, jak Azure Service Fabric udržuje dostupnost služby.

## <a name="availability-of-service-fabric-stateless-services"></a>Dostupnost bezstavových služeb Service Fabric
Service Fabric služby může být stavové nebo bezstavové. Bezstavová služba je aplikační služba, která nemá [místní stav,](service-fabric-concepts-state.md) který musí být vysoce dostupný nebo spolehlivý.

Vytvoření bezstavové služby `InstanceCount`vyžaduje definování . Počet instancí definuje počet instancí aplikační logiky bezstavové služby, které by měly být spuštěny v clusteru. Zvýšení počtu instancí je doporučený způsob škálování bezstavové služby.

Pokud se instance bezstavové pojmenované služby nezdaří, vytvoří se nová instance na způsobilém uzlu v clusteru. Například bezstavová instance služby může selhat na Node1 a znovu vytvořit na Node5.

## <a name="availability-of-service-fabric-stateful-services"></a>Dostupnost stavových služeb Service Fabric
Stavová služba má stav, který je k ní přidružen. V Service Fabric je stavová služba modelována jako sada replik. Každá replika je spuštěná instance kódu služby. Replika má také kopii stavu pro tuto službu. Operace čtení a zápisu jsou prováděny v jedné replice nazývané *Primární*. Změny stavu z operací zápisu jsou *replikovány* do jiných replik v sadě replik, nazývané *Aktivní sekundární operace*, a použity. 

Může existovat pouze jedna primární replika, ale může existovat více aktivních sekundárních replik. Počet aktivních sekundárních replik je konfigurovatelný a vyšší počet replik může tolerovat větší počet souběžných selhání softwaru a hardwaru.

Pokud primární replika přejde dolů, Service Fabric vytvoří jednu z aktivních sekundárních replik nové primární repliky. Tato aktivní sekundární replika již obsahuje aktualizovanou verzi stavu prostřednictvím *replikace*a může pokračovat ve zpracování dalších operací čtení a zápisu. Tento proces se označuje jako *změna konfigurace* a je popsán dále v článku [rekonfigurace.](service-fabric-concepts-reconfiguration.md)

Koncept repliky je primární nebo aktivní sekundární, se označuje jako *role repliky*. Tyto repliky jsou popsány dále v článku [Repliky a instance.](service-fabric-concepts-replica-lifecycle.md) 

## <a name="next-steps"></a>Další kroky
Další informace o konceptech service fabric naleznete v následujících článcích:

- [Škálování služby Service Fabric služby](service-fabric-concepts-scalability.md)
- [Služby partitioning Service Fabric](service-fabric-concepts-partitioning.md)
- [Definování a správa stavu](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

