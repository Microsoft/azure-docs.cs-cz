---
title: Dostupnost služeb Service Fabric | Dokumentace Microsoftu
description: Popisuje detekce selhání, převzetí služeb při selhání a obnovení služby
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: dd10af0d3c8a57168a27a039286ea0ec4c1dad02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60310940"
---
# <a name="availability-of-service-fabric-services"></a>Dostupnost služeb Service Fabric
Tento článek obsahuje základní informace o tom, jak Azure Service Fabric udržuje dostupnosti služby.

## <a name="availability-of-service-fabric-stateless-services"></a>Dostupnost bezstavové služby Service Fabric
Služby Service Fabric může být Stavový nebo bezstavový. Bezstavová služba je aplikační služby, který nemá [místní stavu](service-fabric-concepts-state.md) , který musí být vysoce dostupné a spolehlivé.

Vytvoření bezstavové služby vyžaduje definování `InstanceCount`. Počet instancí definuje počet instancí aplikace logiky bezstavové služby, který by měl být spuštěn v clusteru. Zvýšení počtu instancí je doporučený způsob horizontální navýšení kapacity bezstavovou službu.

Při selhání instance bezstavové služby s názvem, je vytvořena nová instance na oprávněné uzlu v clusteru. Například instance bezstavové služby může selhat v Node1 a znovu vytvořit na počítač Uzel5.

## <a name="availability-of-service-fabric-stateful-services"></a>Dostupnost stavové služby Service Fabric
Stavová služba má stav s ním spojená. V Service Fabric je modelovaná stavové služby jako sady replik. Každá replika je spuštěna instance kódu služby. Replika má také kopii stavu za danou službu. Čtení a zápis, operace se provádějí na jednu repliku, volá se, *primární*. Operace zápisu změn do stavu z *replikovat* do jiné repliky sady replik, volá *aktivní sekundární databáze*a použít. 

Může existovat pouze jedna primární replika, ale může existovat více aktivní sekundární repliky. Je možné konfigurovat počet aktivní sekundární repliky a větší počet replik může tolerovat vyšší počet souběžných softwaru a selhání hardwaru.

Pokud je primární replika přestane fungovat, Service Fabric zajišťuje jeden aktivní sekundární repliky novou primární repliku. Tato aktivní sekundární replika už má aktualizovanou verzi stavu, prostřednictvím *replikace*, a může pokračovat v další zpracování operace čtení a zápisu. Tento proces se označuje jako *Rekonfigurace* a je popsáno dále v [Rekonfigurace](service-fabric-concepts-reconfiguration.md) článku.

Koncept je buď primární, nebo aktivní sekundární repliky se označuje jako *role repliky*. Tyto repliky jsou popsány dále v [replik a instancí](service-fabric-concepts-replica-lifecycle.md) článku. 

## <a name="next-steps"></a>Další postup
Další informace o konceptech Service Fabric najdete v následujících článcích:

- [Škálování služby Service Fabric](service-fabric-concepts-scalability.md)
- [Dělení služeb Service Fabric](service-fabric-concepts-partitioning.md)
- [Definování a správa stavu](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

