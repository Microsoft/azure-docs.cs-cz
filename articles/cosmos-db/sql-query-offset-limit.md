---
title: Klauzule LIMIT POSUNu v Azure Cosmos DB
description: Naučte se používat klauzuli LIMITu POSUNu k přeskočení a provedení určitých hodnot při dotazování v Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 459bd8511577067766cf488f53df57c1dc33fad1
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338286"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Klauzule LIMIT POSUNu v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
  `OFFSET`V klauzuli je vyžadován jak Count, tak `LIMIT` počet `OFFSET LIMIT` . Pokud `ORDER BY` se použije volitelná klauzule, sada výsledků se vytvoří tak, že se přeskočí seřazené hodnoty. V opačném případě bude dotaz vracet pevné pořadí hodnot.

  Poplatek za RU s dotazem se `OFFSET LIMIT` narůstá, protože se zvyšuje počet podmínek, které se posunují. U dotazů, které mají [více stránek výsledků](sql-query-pagination.md), typicky doporučujeme používat [tokeny pro pokračování](sql-query-pagination.md#continuation-tokens). Tokeny pokračování jsou "záložkou" pro místo, kde může dotaz později pokračovat. Použijete `OFFSET LIMIT` -li, není k dispozici žádná "Záložka". Pokud jste chtěli vrátit další stránku dotazu, bude nutné začít od začátku.
  
  Měli byste použít `OFFSET LIMIT` pro případy, kdy byste chtěli zcela přeskočit položky a uložit prostředky klienta. Například byste měli použít, `OFFSET LIMIT` Pokud chcete přeskočit na výsledek dotazu 1000th a nemusíte zobrazit výsledky 1 až 999. Na back-endu `OFFSET LIMIT` pořád načítá každou položku, včetně těch, které se přeskočí. Výhoda výkonu je úspora v klientských prostředcích tím, že se vyhnete nepotřebným zpracování položek.

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
- [Klauzule ORDER BY](sql-query-order-by.md)
