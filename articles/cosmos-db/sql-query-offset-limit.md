---
title: KLAUZULE OFFSET LIMIT v Azure Cosmos DB
description: Naučte se, jak použít klauzuli OFFSET LIMIT přeskočit a vzít některé určité hodnoty při dotazování v Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771572"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>KLAUZULE OFFSET LIMIT v Azure Cosmos DB

Klauzule OFFSET LIMIT je volitelná klauzule přeskočit pak trvat nějaký počet hodnot z dotazu. Počet offset a limit počet jsou požadovány v klauzuli LIMIT OFFSET.

Při limitu offset se používá ve spojení s order by klauzule, sada výsledků je vytvořena tím, že přeskočí a převzít objednané hodnoty. Pokud není použita žádná klauzule ORDER BY, bude mít za následek deterministické pořadí hodnot.

## <a name="syntax"></a>Syntaxe
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumenty

- `<offset_amount>`

   Určuje počet položek celé číslo, které by měly výsledky dotazu přeskočit.

- `<limit_amount>`
  
   Určuje počet položek celé číslo, které by měly výsledky dotazu obsahovat.

## <a name="remarks"></a>Poznámky
  
  `OFFSET` Počet a počet `LIMIT` jsou požadovány `OFFSET LIMIT` v klauzuli. Pokud je `ORDER BY` použita volitelná klauzule, sada výsledků je vytvořena provedením přeskočení objednaných hodnot. V opačném případě dotaz vrátí pevné pořadí hodnot.

  ŽP poplatek dotazu se `OFFSET LIMIT` zvýší s počtem termínů, které jsou offset zvyšuje. Pro dotazy, které mají více stránek výsledků, obvykle doporučujeme používat tokeny pokračování. Tokeny pokračování jsou "záložkou" pro místo, kde může dotaz později pokračovat. Pokud používáte `OFFSET LIMIT`, neexistuje žádná "záložka". Pokud chcete vrátit další stránku dotazu, budete muset začít od začátku.
  
  Měli byste `OFFSET LIMIT` použít pro případy, kdy chcete přeskočit dokumenty úplně a uložit prostředky klienta. Například byste měli `OFFSET LIMIT` použít, pokud chcete přeskočit na 1000th výsledek dotazu a nemají potřebu zobrazit výsledky 1 až 999. V back-endu `OFFSET LIMIT` stále načte každý dokument, včetně těch, které jsou přeskočeny. Výhoda výkonu je úspora prostředků klienta tím, že se vyhnete zpracování dokumentů, které nejsou potřeba.

## <a name="examples"></a>Příklady

Například je zde dotaz, který přeskočí první hodnotu a vrátí druhou hodnotu (v pořadí podle názvu města bydliště):

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
