---
title: Odstranění dat z Azure Průzkumník dat
description: Tento článek popisuje scénáře odstranění ve službě Azure Průzkumník dat, včetně mazání, odstraňování rozsahů a odstraňování založeného na uchovávání.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 681cfd71d2666630b192935d66ba32eaf16c92de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204609"
---
# <a name="delete-data-from-azure-data-explorer"></a>Odstranění dat z Azure Průzkumník dat

Azure Průzkumník dat podporuje různé scénáře odstranění popsané v tomto článku. 

## <a name="delete-data-using-the-retention-policy"></a>Odstranění dat pomocí zásad uchovávání informací

Azure Průzkumník dat automaticky odstraňuje data na základě [zásad uchovávání informací](/azure/kusto/management/retentionpolicy). Tato metoda představuje nejúčinnější a bezproblémový způsob odstraňování dat. Nastavte zásady uchovávání informací na úrovni databáze nebo tabulky.

Vezměte v úvahu databázi nebo tabulku, která je nastavená na 90 dnů uchovávání. Pokud potřebujete jenom 60 dní dat, odstraňte starší data následujícím způsobem:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Odstranění dat vyřazením rozsahů

[Rozsah (data horizontálních oddílů)](/azure/kusto/management/extents-overview) je interní struktura, kde jsou data uložená. Každý rozsah může obsahovat až milionů záznamů. Rozsahy je možné odstranit jednotlivě nebo jako skupinu pomocí [příkazů rozsahů přetažení](/azure/kusto/management/extents-commands#drop-extents). 

### <a name="examples"></a>Příklady

Můžete odstranit všechny řádky v tabulce nebo pouze konkrétní rozsah.

* Odstranit všechny řádky v tabulce:

    ```kusto
    .drop extents from TestTable
    ```

* Odstranění určitého rozsahu:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Odstranění jednotlivých řádků pomocí vyprázdnění

K odstranění řádků jednotlivců lze použít [vyprázdnění dat](/azure/kusto/management/data-purge) . Odstranění není okamžité a vyžaduje významné systémové prostředky. V takovém případě se doporučuje jenom pro scénáře dodržování předpisů.  

