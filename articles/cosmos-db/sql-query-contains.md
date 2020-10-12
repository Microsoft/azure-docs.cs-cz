---
title: Obsahuje v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si, jak funkce CONTAINS SQL System v Azure Cosmos DB vrátí logickou hodnotu, která označuje, zda první řetězcový výraz obsahuje sekundu.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4877272fc2db521977a4111317118380399d27c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84322699"
---
# <a name="contains-azure-cosmos-db"></a>OBSAHUJE (Azure Cosmos DB)

Vrátí logickou hodnotu, která označuje, zda první řetězcový výraz obsahuje sekundu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Je řetězcový výraz, který má být prohledán.  
  
*str_expr2*  
   Je řetězcový výraz, který se má najít.  

*bool_expr* Volitelná hodnota pro ignorování velkých a malých písmen Pokud je nastaveno na hodnotu true, obsahuje se při hledání bez rozlišování velkých a malých písmen. Je-li tento parametr zadán, je tato hodnota false.
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad zkontroluje, zda "ABC" obsahuje "AB" a "ABC" obsahuje "A".  
  
```sql
SELECT CONTAINS("abc", "ab", false) AS c1, CONTAINS("abc", "A", false) AS c2, CONTAINS("abc", "A", true) AS c3
```  
  
 Zde je sada výsledků.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude využívat výhod [indexu rozsahu](index-policy.md#includeexclude-strategy).

Spotřeba RU z obsahuje se zvýší, protože se zvýší mohutnost vlastnosti ve funkci systému. Jinými slovy, Pokud kontrolujete, zda hodnota vlastnosti obsahuje určitý řetězec, poplatek za dotaz na RU bude záviset na počtu možných hodnot pro danou vlastnost.

Zvažte například dvě vlastnosti: město a země. Mohutnost města je 5 000 a mohutnost země je 200. Tady jsou dva příklady dotazů:

```sql
    SELECT * FROM c WHERE CONTAINS(c.town, "Red", false)
```

```sql
    SELECT * FROM c WHERE CONTAINS(c.country, "States", false)
```

První dotaz bude pravděpodobně používat více ru než druhý dotaz, protože mohutnost města je vyšší než země.

Pokud je velikost vlastnosti v poli obsahuje více než 1 KB pro některé dokumenty, stroj dotazů bude potřebovat tyto dokumenty načíst. V takovém případě dotazovací stroj nebude moci plně vyhodnotit, pokud obsahuje index. Pokud máte velký počet dokumentů s velikostmi vlastností větší než 1 KB, bude poplatek za RU v rámci obsahovat vysoký.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
