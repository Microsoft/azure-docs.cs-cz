---
title: Klauzuli LIMIT posun ve službě Azure Cosmos DB
description: Další informace o klauzuli OFFSET omezení pro službu Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 60ac28c80e9f7cc72f4d6005c12cb5f68671341e
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342954"
---
# <a name="offset-limit-clause"></a>Klauzule posun omezení

V klauzuli LIMIT posun je volitelná klauzule přeskočit pak provést některé hodnoty z dotazu. Počet POSUNUTÍ a počtu LIMIT se vyžadují v klauzuli LIMIT posun.

Při posunu omezení se používá ve spojení s klauzulí ORDER BY, sadu výsledků dotazu je vytvořen tímto způsobem přeskočit a provádět s nimi seřazené hodnoty. Pokud se používá bez klauzule ORDER by, způsobí deterministické pořadí hodnot.

## <a name="syntax"></a>Syntaxe
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumenty

- `<offset_amount>`

   Určuje celočíselný počet položek, které by měl přeskočit výsledky dotazu.

- `<limit_amount>`
  
   Určuje celočíselný počet položek, které by měla obsahovat výsledky dotazu

## <a name="remarks"></a>Poznámky
  
  Počet POSUNUTÍ i počet omezení se vyžadují v klauzuli LIMIT posun. Pokud volitelný `ORDER BY` klauzule, sadu výsledků dotazu je vytvořen tímto způsobem přeskočení přes seřazený hodnoty. V opačném případě dotaz vrátí pevné pořadí hodnot. Momentálně tuto klauzuli je podporovaný pro dotazy v rámci pouze jednoho oddílu, dotazy napříč oddíly se zatím nepodporují.

## <a name="examples"></a>Příklady

Tady je příklad dotaz, který přeskočí první hodnotu a vrátí hodnotu druhého (v pořadí podle název rezidenční města):

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

Tady je dotaz, který přeskočí první hodnotu a vrátí hodnotu druhého (bez řazení):

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

## <a name="next-steps"></a>Další postup

- [Začínáme](sql-query-getting-started.md)
- [Klauzule SELECT](sql-query-select.md)
- [ORDER BY – klauzule](sql-query-order-by.md)
