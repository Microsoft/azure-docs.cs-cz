---
title: Výhody více hlavních hlavních bodů Azure Cosmos DB
description: Seznamte se s výhodami více hlavních serverů v Azure Cosmos DB, porovnáním požadavků na latenci a sla v jednom a více umístěních pro zápis.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872005"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Principy výhod více hlavních v Azure Cosmos DB

Provozovatelé účtů Cosmos DB by měli zvolit vhodnou konfiguraci globální distribuce, aby zajistili požadavky na latenci, dostupnost a RTO pro své aplikace. Účty Azure Cosmos nakonfigurované s více umístěními zápisu nabízejí významné výhody oproti účtům s jedním umístěním zápisu, včetně 99,999% dostupnosti zápisu SLA, <10 ms latence zápisu sla na 99 percentilu a RTO = 0 v regionální havárii.

## <a name="comparison-of-features"></a>Porovnání funkcí

|Požadavek na aplikaci|Více umístění zápisu|Umístění jednoho zápisu|Poznámka|
|---|---|---|---|
|Zapsat latenci SLA <10ms na P99|**Ano**|Ne|Účty s umístěním jednoho zápisu vznikají další latence sítě mezi oblastmi pro každý zápis.|
|Čtení latence SLA <10ms na P99|**Ano**|Ano| |
|Napište SLA 99.999%|**Ano**|Ne|Účty se zárukou s jednou lokací zápisu ve výši 99,99 %|
|RTO = 0|**Ano**|Ne|Nulová prostoje pro zápisy v případě regionálních katastrof. Účty s jedním umístěním zápisu mají RTO 15 min.|

## <a name="next-steps"></a>Další kroky

Pokud chcete stále zakázat EnableMultipleWriteLocations ve vašem účtu Azure Cosmos, [otevřete lístek podpory](https://azure.microsoft.com/support/create-ticket/).
