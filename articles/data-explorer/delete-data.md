---
title: Odstranění dat z Průzkumníka dat Azure
description: Tento článek popisuje scénáře odstranění v Průzkumníku dat Azure, včetně vymazání, uvolnění rozsahů a odstranění na základě uchovávání informací.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: dd0f8740d148a7817bcfe2fbad591ceeb1610d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501411"
---
# <a name="delete-data-from-azure-data-explorer"></a>Odstranění dat z Průzkumníka dat Azure

Azure Data Explorer podporuje různé scénáře odstranění popsané v tomto článku. 

## <a name="delete-data-using-the-retention-policy"></a>Odstranění dat pomocí zásad uchovávání informací

Azure Data Explorer automaticky odstraní data na základě [zásad uchovávání informací](/azure/kusto/management/retentionpolicy). Tato metoda je nejúčinnější a bezproblémový způsob odstranění dat. Nastavte zásady uchovávání informací na úrovni databáze nebo tabulky.

Zvažte databázi nebo tabulku, která je nastavena na 90 dnů uchovávání. Pokud jsou potřeba pouze 60 denní data, odstraňte starší data následujícím způsobem:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Odstranění dat uvolněním rozsahu

[Rozsah (střep dat)](/azure/kusto/management/extents-overview) je vnitřní struktura, kde jsou uložena data. Každý rozsah může pojmout až miliony záznamů. Rozsahy lze odstranit jednotlivě nebo jako skupinu pomocí [příkazů rozsahu přetažení](/azure/kusto/management/extents-commands#drop-extents). 

### <a name="examples"></a>Příklady

Můžete odstranit všechny řádky v tabulce nebo jen určitý rozsah.

* Odstranit všechny řádky v tabulce:

    ```kusto
    .drop extents from TestTable
    ```

* Odstranit určitý rozsah:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Odstranění jednotlivých řádků pomocí proplachování

[Vymazání dat](/azure/kusto/concepts/data-purge) lze použít k odstranění řádků jednotlivců. Odstranění není okamžité a vyžaduje významné systémové prostředky. Jako takový, je to jen doporučuje pro scénáře dodržování předpisů.  

