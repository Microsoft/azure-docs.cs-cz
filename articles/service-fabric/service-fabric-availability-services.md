---
title: Dostupnost služeb Service Fabric Services
description: Popisuje detekci chyb, převzetí služeb při selhání a obnovení služby v aplikaci Azure Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018898"
---
# <a name="availability-of-service-fabric-services"></a>Dostupnost služeb Service Fabric Services
Tento článek poskytuje přehled o tom, jak Azure Service Fabric udržuje dostupnost služby.

## <a name="availability-of-service-fabric-stateless-services"></a>Dostupnost bezstavových služeb Service Fabric
Služba Service Fabric Services může být buď stavová, nebo Bezstavová. Bezstavová služba je Aplikační služba, která nemá [místní stav](service-fabric-concepts-state.md) , který musí být vysoce dostupný nebo spolehlivý.

Vytvoření bezstavové služby vyžaduje definování `InstanceCount` . Počet instancí definuje počet instancí aplikační logiky bez stavové služby, které by měly být spuštěny v clusteru. Zvýšení počtu instancí je doporučený způsob škálování bezstavové služby.

V případě, že dojde k neúspěšnému výskytu instance pojmenované služby, vytvoří se nová instance v oprávněném uzlu v clusteru. Například instance bezstavové služby může selhat na Uzel1 a znovu vytvořit v počítač Uzel5.

## <a name="availability-of-service-fabric-stateful-services"></a>Dostupnost stavových služeb Service Fabric
Stavová služba je přidružená k ní. V Service Fabric je stavová služba modelována jako sada replik. Každá replika je spuštěná instance kódu služby. Replika má také kopii stavu pro tuto službu. Operace čtení a zápisu se provádějí v jedné replice, která se nazývá *primární*. Změny stavu z operací zápisu se *replikují* do ostatních replik v sadě repliky, označované jako *aktivní sekundární* a použité. 

Může existovat jenom jedna primární replika, ale může existovat několik aktivních sekundárních replik. Počet aktivních sekundárních replik je konfigurovatelný a vyšší počet replik může tolerovat větší počet souběžných selhání softwaru a hardwaru.

Pokud dojde k výpadku primární repliky, Service Fabric vytvoří jednu z aktivních sekundárních replik nové primární repliky. Tato aktivní sekundární replika již má aktualizovanou verzi stavu prostřednictvím *replikace* a může pokračovat ve zpracování dalších operací čtení a zápisu. Tento proces se označuje jako *rekonfigurace* a je podrobněji popsaný v článku o [Překonfiguraci](service-fabric-concepts-reconfiguration.md) .

Koncept repliky, která je buď primární nebo aktivní sekundární, se označuje jako *role repliky*. Tyto repliky jsou podrobněji popsány v článku [repliky a instance](service-fabric-concepts-replica-lifecycle.md) . 

## <a name="next-steps"></a>Další kroky
Další informace o Service Fabric konceptech najdete v následujících článcích:

- [Škálování služby Service Fabric Services](service-fabric-concepts-scalability.md)
- [Dělení Service Fabric služeb](service-fabric-concepts-partitioning.md)
- [Definování a Správa stavu](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

