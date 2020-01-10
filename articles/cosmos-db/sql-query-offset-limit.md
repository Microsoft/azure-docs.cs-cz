---
title: Klauzule LIMIT POSUNu v Azure Cosmos DB
description: Naučte se používat klauzuli LIMITu POSUNu k přeskočení a provedení určitých hodnot při dotazování v Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: a8df220be211c3c8d8cdeab8a8aebfd35e77ebf8
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732582"
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
  
  V klauzuli LIMIT POSUNu je vyžadován jak počet POSUNUTí, tak počet omezení. Pokud je použita volitelná klauzule `ORDER BY`, sada výsledků se vytvoří tak, že přeskočí seřazené hodnoty. V opačném případě bude dotaz vracet pevné pořadí hodnot. Tato klauzule se teď podporuje pro dotazy v rámci jednoho oddílu a také pro Mezioddílové dotazy.

## <a name="examples"></a>Příklady

Zde je například dotaz, který přeskočí první hodnotu a vrátí druhou hodnotu (v pořadí podle názvu rezidentního města):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Výsledky jsou následující:

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

Výsledky jsou následující:

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
- [ORDER BY – klauzule](sql-query-order-by.md)
