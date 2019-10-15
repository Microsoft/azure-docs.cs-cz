---
title: Klauzule LIMIT POSUNu v Azure Cosmos DB
description: Přečtěte si o klauzuli LIMITu POSUNu pro Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 7aae56783f83f13b50321c88d69f07d910e589dd
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326877"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Klauzule LIMIT POSUNu v Azure Cosmos DB

Klauzule LIMIT POSUNu je volitelná klauzule, která se má přeskočit a pak z dotazu převezme určitý počet hodnot. V klauzuli LIMIT POSUNu jsou požadovány počty POSUNUTí a počet omezení.

Pokud se LIMIT POSUNu používá ve spojení s klauzulí ORDER BY, sada výsledků se vytvoří tak, že se přeskočí a převezmou seřazené hodnoty. Pokud se nepoužije žádná klauzule ORDER BY, výsledkem bude deterministické pořadí hodnot.

## <a name="syntax"></a>Syntaxe
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumenty

- `<offset_amount>`

   Určuje celočíselný počet položek, které by měly přeskočit výsledky dotazu.

- `<limit_amount>`
  
   Určuje celočíselný počet položek, které by měly obsahovat výsledky dotazu.

## <a name="remarks"></a>Poznámky
  
  V klauzuli LIMIT POSUNu je vyžadován jak počet POSUNUTí, tak počet omezení. Pokud se použije volitelná klauzule `ORDER BY`, sada výsledků se vytvoří tak, že se přeskočí seřazené hodnoty. V opačném případě bude dotaz vracet pevné pořadí hodnot. V současné době je tato klauzule podporována pro dotazy pouze v rámci jednoho oddílu, dotazy na více oddílů je ještě nepodporují.

## <a name="examples"></a>Příklady

Zde je například dotaz, který přeskočí první hodnotu a vrátí druhou hodnotu (v pořadí podle názvu rezidentního města):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Výsledky jsou:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Zde je dotaz, který přeskočí první hodnotu a vrátí druhou hodnotu (bez řazení):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Výsledky jsou:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Klauzule SELECT](sql-query-select.md)
- [Klauzule ORDER BY](sql-query-order-by.md)
