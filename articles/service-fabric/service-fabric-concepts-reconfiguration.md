---
title: Změna konfigurace ve službě Azure Fabric
description: Informace o konfiguracích pro repliky stavové služby a proces ustavování service fabric používá k udržení konzistence a dostupnosti během změny.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: bd46a7776495624affef77a44fcf68334750ba17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609991"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Změna konfigurace ve službě Azure Fabric
*Konfigurace* je definována jako repliky a jejich role pro oddíl stavové služby.

*Změna konfigurace* je proces přesunutí jedné konfigurace do jiné konfigurace. Provede změnu sady replik pro oddíl stavové služby. Stará konfigurace se nazývá *předchozí konfigurace (PC)* a nová konfigurace se nazývá *aktuální konfigurace (CC)*. Reconfiguration protocol in Azure Service Fabric zachovává konzistenci a udržuje dostupnost během všech změn sady replik.

Správce převzetí služeb při selhání iniciuje změny konfigurace v reakci na různé události v systému. Například pokud primární selže pak je zahájena změna konfigurace na podporu aktivní sekundární primární. Dalším příkladem je v reakci na upgrady aplikací, kdy může být nutné přesunout primární do jiného uzlu za účelem upgradu uzlu.

## <a name="reconfiguration-types"></a>Typy rekonfigurace
Rekonfigurace lze rozdělit do dvou typů:

- Změny konfigurace, kde se mění primární:
    - **Převzetí služeb při selhání**: Převzetí služeb při selhání jsou rekonfigurace v reakci na selhání spuštěné primární.
    - **SwapPrimary**: Swaps jsou rekonfigurace, kde Service Fabric potřebuje přesunout spuštěné primární z jednoho uzlu do druhého, obvykle v reakci na vyrovnávání zatížení nebo upgrade.

- Změny konfigurace, kde primární se nemění.

## <a name="reconfiguration-phases"></a>Fáze změny konfigurace
Změna konfigurace probíhá v několika fázích:

- **Fáze 0**: Tato fáze probíhá v přenocování primární konfigurace swap, kde aktuální primární přenáší svůj stav na nové primární a přechody na aktivní sekundární.

- **Fáze1**: Tato fáze probíhá během rekonfigurace, kde se mění primární. Během této fáze Service Fabric identifikuje správné primární mezi aktuální repliky. Tato fáze není potřeba během změny konfigurace prohození primární, protože nové primární již byla vybrána. 

- **Fáze 2**: Během této fáze Service Fabric zajišťuje, že všechna data jsou k dispozici ve většině replik aktuální konfigurace.

Existuje několik dalších fází, které jsou pouze pro interní použití.

## <a name="stuck-reconfigurations"></a>Zablokované rekonfigurace
Rekonfigurace se mohou *zaseknout* z různých důvodů. Některé z běžných důvodů patří:

- **Dolů repliky**: Některé fáze změny konfigurace vyžadují většinu replik v konfiguraci být nahoru.
- **Problémy se sítí nebo komunikací**: Rekonfigurace vyžadují připojení k síti mezi různými uzly.
- **Selhání rozhraní API**: Protokol pro změnu konfigurace vyžaduje, aby implementace služby dokončily určitá rozhraní API. Například není ctít zrušení token ve spolehlivé službě způsobí SwapPrimary rekonfigurace uvíznou.

Pomocí sestav stavu ze systémových součástí, jako jsou System.FM, System.RA a System.RAP, můžete diagnostikovat, kde se změna konfigurace zasekla. [Stránka sestavy stavu systému](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) popisuje tyto sestavy stavu.

## <a name="next-steps"></a>Další kroky
Další informace o konceptech service fabric naleznete v následujících článcích:

- [Životní cyklus Reliable Services – C#](service-fabric-reliable-services-lifecycle.md)
- [Sestavy stavu systému](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Repliky a instance](service-fabric-concepts-replica-lifecycle.md)
