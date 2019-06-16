---
title: Změny konfigurace v Azure Service Fabric | Dokumentace Microsoftu
description: Vysvětlení Rekonfigurace oddílů v Service Fabric
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60882193"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Změny konfigurace v Azure Service Fabric
A *konfigurace* je definován jako repliky a jejich rolí pro rozdělení stavové služby.

A *Rekonfigurace* je proces přechodu jedna konfigurace do jiné konfigurace. Odešle změnu sady replik pro rozdělení stavové služby. Původní konfigurace se nazývá *předchozí konfiguraci (PC)* , a aby se nová konfigurace se nazývá *aktuální konfiguraci (kopie)* . Protokol změny konfigurace v Azure Service Fabric zachová konzistence a udržuje dostupnost během změny do replik.

Správce převzetí služeb při selhání iniciuje rekonfigurací v reakci na různé události v systému. Například pokud selže primární pak Rekonfigurace je zahájeno podporovat aktivní sekundární do primární. Dalším příkladem je v reakci na upgrady aplikací, když bude pravděpodobně nutné přesunout do jiného uzlu primární před upgradem uzlu.

## <a name="reconfiguration-types"></a>Typy rekonfigurace
Rekonfigurací je možné rozdělit do dvou typů:

- Pokud se mění primární rekonfigurací:
    - **Převzetí služeb při selhání**: Převzetí služeb při selhání jsou rekonfigurací v reakci na selhání primární spuštěné.
    - **SwapPrimary**: Záměna jsou rekonfigurací, kde je třeba přesunout spuštěný primární v z jednoho uzlu do jiného, obvykle v reakci na Vyrovnávání zatížení Service Fabric nebo upgradu.

- Pokud není změněn primární rekonfigurací.

## <a name="reconfiguration-phases"></a>Fáze Rekonfigurace
Rekonfigurace pokračuje v několika fázích:

- **Phase0**: Tato fáze probíhá prohození primárního rekonfigurací kde aktuální primární přenese jeho stav na nový primární a přechody do aktivní sekundární.

- **Phase1**: Tato fáze se stane během rekonfigurací, ve kterém se mění primární. V této fázi Service Fabric identifikuje správné primární mezi aktuálním repliky. Tato fáze není nutné během rekonfigurací primární odkládacího souboru, protože nový primární již byla vybrána. 

- **Údajů Fáze 2**: V této fázi Service Fabric zajišťuje, že všechna data k dispozici ve většině repliky aktuální konfiguraci.

Existuje několik fází, které jsou jen pro interní použití.

## <a name="stuck-reconfigurations"></a>Zablokované rekonfigurací
Můžete získat rekonfigurací *zablokované* pro celou řadu důvodů. Mezi běžné příčiny patří:

- **Dolů repliky**: Fáze některé konfigurace vyžadují většinou repliky v konfiguraci dá zvládnout.
- **Síť nebo komunikační problémy**: Rekonfigurací vyžadují připojení k síti mezi různými uzly.
- **Rozhraní API selhání**: Změna konfigurace protokolu vyžaduje, že implementace služby dokončit určitých rozhraní API. Například není dodržením token zrušení v spolehlivé služby způsobí, že SwapPrimary rekonfigurací zaseknou.

Použití sestav o stavu ze systémových komponent, jako je například System.FM – System.RA a System.RAP, se zasekne diagnostikovat tam, kde změny konfigurace. [Stránku sestavy stavu systému](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) popisuje tyto sestavy stavu.

## <a name="next-steps"></a>Další postup
Další informace o konceptech Service Fabric najdete v následujících článcích:

- [Životní cyklus Reliable Services – C#](service-fabric-reliable-services-lifecycle.md)
- [Systémovými stavovými sestavami](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Repliky a instance](service-fabric-concepts-replica-lifecycle.md)
