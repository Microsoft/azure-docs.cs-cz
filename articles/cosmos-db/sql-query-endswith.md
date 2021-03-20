---
title: EndsWith v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci ENDSWITH SQL System v Azure Cosmos DB a vrátí logickou hodnotu, která označuje, jestli první řetězcový výraz končí druhým.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0cc93fee8aacc711a797925cb2e2808b73cafd1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93338828"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Vrátí logickou hodnotu, která označuje, zda první řetězcový výraz končí druhým.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Je řetězcový výraz.  
  
*str_expr2*  
   Je řetězcový výraz, který má být porovnán s koncem *str_expr1*.

*bool_expr* Volitelná hodnota pro ignorování velkých a malých písmen Když je nastaveno na hodnotu true, ENDSWITH provede vyhledávání bez rozlišování velkých a malých písmen. Je-li tento parametr zadán, je tato hodnota false.
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
Následující příklad zkontroluje, zda řetězec "ABC" končí řetězcem "b" a "bC".  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 Zde je sada výsledků.  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude využívat výhod [indexu rozsahu](index-policy.md#includeexclude-strategy).

Využití RU pro EndsWith se zvýší, protože se zvýší mohutnost vlastnosti v systémové funkci. Jinými slovy, Pokud kontrolujete, zda hodnota vlastnosti končí určitým řetězcem, poplatek za dotaz na RU bude záviset na počtu možných hodnot pro danou vlastnost.

Zvažte například dvě vlastnosti: město a země. Mohutnost města je 5 000 a mohutnost země je 200. Tady jsou dva příklady dotazů:

```sql
    SELECT * FROM c WHERE ENDSWITH(c.town, "York", false)
```

```sql
    SELECT * FROM c WHERE ENDSWITH(c.country, "States", false)
```

První dotaz bude pravděpodobně používat více ru než druhý dotaz, protože mohutnost města je vyšší než země.

Pokud je velikost vlastnosti v EndsWith větší než 1 KB u některých dokumentů, bude tento dotazovací stroj potřebovat tyto dokumenty načíst. V takovém případě dotazovací stroj nebude moci plně vyhodnotit EndsWith pomocí indexu. Náklady na RU za EndsWith budou vysoké, pokud máte velký počet dokumentů s velikostí vlastností větší než 1 KB.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
