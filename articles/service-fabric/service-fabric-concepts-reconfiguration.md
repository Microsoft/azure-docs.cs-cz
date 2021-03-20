---
title: Rekonfigurace v Azure Service Fabric
description: Přečtěte si o konfiguracích pro repliky stavových služeb a o procesu Service Fabric změny konfigurace, který se používá k udržení konzistence a dostupnosti při změně.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: bd46a7776495624affef77a44fcf68334750ba17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "75609991"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Rekonfigurace v Azure Service Fabric
*Konfigurace* je definovaná jako repliky a jejich role pro oddíl stavové služby.

*Překonfigurování* je proces přesunutí jedné konfigurace do jiné konfigurace. Provede změnu sady replik pro oddíl stavové služby. Stará konfigurace se nazývá *předchozí konfigurace (počítač)* a nová konfigurace se nazývá *aktuální konfigurace (CC)*. Protokol rekonfigurace v Azure Service Fabric zachovává konzistenci a udržuje dostupnost během jakýchkoli změn v sadě replik.

Správce převzetí služeb při selhání iniciuje překonfigurování v reakci na různé události v systému. Například pokud primární databáze dojde k chybě, inicializuje se znovu konfigurace, která zvýší úroveň aktivní sekundární na primární. Dalším příkladem je odezva na upgrady aplikace, kdy může být nutné přesunout primární uzel do jiného uzlu, aby bylo možné uzel upgradovat.

## <a name="reconfiguration-types"></a>Typy rekonfigurace
Rekonfigurace je možné klasifikovat do dvou typů:

- Změny konfigurace, u kterých probíhá změna primárního:
    - **Převzetí služeb při** selhání: převzetí služeb při selhání je rekonfigurace v reakci na selhání spuštěné primární služby.
    - **SwapPrimary**: prohození jsou rekonfigurace, kde Service Fabric musí přesunout běžící primární uzel z jednoho uzlu do druhého, obvykle v reakci na Vyrovnávání zatížení nebo upgrade.

- Změny konfigurace, u kterých se primární primární nemění.

## <a name="reconfiguration-phases"></a>Fáze rekonfigurace
Překonfigurování pokračuje v několika fázích:

- **Phase0**: k této fázi dochází v prohození primárních konfigurací, kde aktuální primární primární přenos svého stavu na nový primární a přechod na aktivní sekundární.

- **Fáze 1**: Tato fáze probíhá během rekonfigurace, kdy se primární změna mění. V průběhu této fáze Service Fabric identifikuje správnou primární repliku mezi aktuálními replikami. Tato fáze není během přeměněných primárních konfigurací nutná, protože nová primární již byla vybrána. 

- **Phase2**: v této fázi Service Fabric zajišťuje, aby všechna data byla k dispozici ve většině replik aktuální konfigurace.

K dispozici je několik dalších fází, které jsou pouze pro interní použití.

## <a name="stuck-reconfigurations"></a>Zablokované rekonfigurace
Rekonfigurace se může *zablokovat* z nejrůznějších důvodů. Mezi běžné příčiny patří:

- **Nefunkční repliky**: některé fáze překonfigurování vyžadují většinu replik v konfiguraci.
- **Problémy se sítí nebo komunikací**: překonfigurování vyžadují síťové připojení mezi různými uzly.
- **Selhání rozhraní API**: protokol rekonfigurace vyžaduje, aby implementace služby dokončila určitá rozhraní API. Například nedodržení tokenu zrušení ve spolehlivé službě způsobí, že se SwapPrimary rekonfigurace zablokování.

Pomocí sestav o stavu ze systémových součástí, jako jsou System.FM, System. RA a System. RAP, můžete diagnostikovat, kde je znovu zablokovaná rekonfigurace. [Stránka sestavy stav systému](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) popisuje tyto zprávy o stavu.

## <a name="next-steps"></a>Další kroky
Další informace o Service Fabric konceptech najdete v následujících článcích:

- [Životní cyklus Reliable Services – C#](service-fabric-reliable-services-lifecycle.md)
- [Sestavy stavu systému](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Repliky a instance](service-fabric-concepts-replica-lifecycle.md)
