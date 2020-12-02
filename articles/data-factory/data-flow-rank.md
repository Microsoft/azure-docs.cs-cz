---
title: Transformace pořadí v toku dat mapování
description: Jak použít transformaci pořadí toku dat mapování Azure Data Factory generování sloupce řazení
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/05/2020
ms.openlocfilehash: b7adb6bf13cba5f886b442515e8ba5661cfeb8ef
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490920"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>Transformace pořadí v toku dat mapování 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pomocí transformace Rank můžete vygenerovat seřazené řazení na základě podmínek řazení zadaných uživatelem. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>Konfigurace

![Nastavení řazení](media/data-flow/rank-configuration.png "Nastavení řazení")

**Nerozlišuje velká a malá písmena:** Pokud je sloupec řazení typu String, bude se v tomto pořadí přiřazovat. 

**Hustý:** Pokud je tato možnost povolená, bude mít sloupec Rank Zúžený rozsah. Každý počet pořadí bude po sobě jdoucí číslicí a hodnoty pořadí se po propojení nepřeskočí.

**Sloupec Rank:** Název vygenerovaného sloupce klasifikace Tento sloupec bude typu Long.

**Podmínky řazení:** Vyberte sloupce, podle kterých řadíte a v jakém pořadí se má řazení provést. Pořadí určuje prioritu řazení.

Výše uvedená konfigurace přijímá příchozí basketbalový data a vytváří sloupec Rank s názvem "pointsRanking". Řádek s nejvyšší hodnotou ve sloupci *PTS* bude mít hodnotu *pointsRanking* 1.

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntax

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Příklad

![Nastavení řazení](media/data-flow/rank-configuration.png "Nastavení řazení")

Skript toku dat pro výše uvedenou konfiguraci řazení je v následujícím fragmentu kódu.

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>Další kroky

Filtruje řádky podle hodnot pořadí pomocí [transformace filtru](data-flow-filter.md).
