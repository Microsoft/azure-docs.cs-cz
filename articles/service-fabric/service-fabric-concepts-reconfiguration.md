---
title: Změny konfigurace v Azure Service Fabric | Microsoft Docs
description: Pochopení Rekonfigurace oddílů v Service Fabric
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: a24aa6aa1695a3d1166816b7960bdd7b551e1a37
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Změny konfigurace v Azure Service Fabric
A *konfigurace* je definován jako repliky a jejich rolí pro oddílu stavové služby.

A *Rekonfigurace* je proces přechodu do jiné konfigurace jednu konfiguraci. Umožňuje změnu do sady replik pro oddílu stavové služby. Původní konfigurace se nazývá *předchozí konfiguraci (počítače)*, a nová konfigurace se nazývá *aktuální konfiguraci (kopie)*. Protokol změny konfigurace v Azure Service Fabric zachovává konzistence a udržuje dostupnosti během změny do sady replik.

Převzetí služeb při selhání správce spustí že změny konfigurace v reakci na různé události v systému. Například pokud selže primární pak změny konfigurace se zahájí podporovat aktivní sekundární primárnímu. Dalším příkladem je v reakci na upgrady aplikací, když může být nezbytné k přesunutí primární před upgradem uzlu do jiného uzlu.

## <a name="reconfiguration-types"></a>Změna konfigurace typy
Že změny konfigurace lze rozdělit do dvou typů:

- Že změny konfigurace, kde je primární změna:
    - **Převzetí služeb při selhání**: převzetí služeb při selhání se, že změny konfigurace v reakci na selhání primární spuštěný.
    - **SwapPrimary**: záměna se, že změny konfigurace, kde je třeba přesunout a spuštění primární v z jednoho uzlu do druhého, obvykle v reakci na Vyrovnávání zatížení Service Fabric nebo upgrade.

- Že změny konfigurace, které není primární změna.

## <a name="reconfiguration-phases"></a>Změna konfigurace fáze
Rekonfigurace pokračuje v několika fází:

- **Phase0**: Tato fáze probíhá prohození primární že změny konfigurace, kde aktuální primární přenese na nový primární a přechody do aktivní sekundární jeho stav.

- **Fáze 1**: Tato fáze probíhá při že změny konfigurace, kde je změna primární. V průběhu této fáze identifikuje Service Fabric správné primární mezi aktuální repliky. Tato fáze není nutné během swap primární že změny konfigurace, protože nový primární již byla vybrána. 

- **Údajů Fáze 2**: v průběhu této fáze Service Fabric zajišťuje, že všechna data k dispozici v většinou replik aktuální konfiguraci.

Existuje několik fází, které jsou pouze pro interní použití.

## <a name="stuck-reconfigurations"></a>Zablokované že změny konfigurace
Že změny konfigurace můžete získat *zablokované* z různých důvodů. Mezi běžné příčiny patří:

- **Dolů repliky**: fáze některé změny konfigurace vyžaduje většinu repliky v konfiguraci jako nahoru.
- **Síť nebo komunikační problémy**: že změny konfigurace vyžaduje připojení k síti mezi různými uzly.
- **Rozhraní API selhání**: protokol Rekonfigurace vyžaduje, aby implementace služby dokončit určitých rozhraní API. Například není ctít zásady token zrušení v spolehlivě způsobí, že změny konfigurace SwapPrimary uvíznutí.

Pomocí sestav stavu z komponent systému, například System.FM, System.RA a System.RAP, je při diagnostice tam, kde Rekonfigurace zablokované. [Stránka sestavy stavu systému](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) popisuje tyto sestavy stavu.

## <a name="next-steps"></a>Další postup
Další informace o konceptech Service Fabric najdete v následujících článcích:

- [Životní cyklus Reliable Services – C#](service-fabric-reliable-services-lifecycle.md)
- [Sestav o stavu systému](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Repliky a instance](service-fabric-concepts-replica-lifecycle.md)
