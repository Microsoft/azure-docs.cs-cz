---
title: Klauzule WHERE v Azure Cosmos DB
description: Seznamte se s klauzulí WHERE SQL pro Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: cd5643d8be06afcd43c5bfe38d6f5e9caa6f906e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326642"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Klauzule WHERE v Azure Cosmos DB

Volitelná klauzule WHERE (`WHERE <filter_condition>`) určuje podmínky, které musí zdrojové položky JSON splňovat, aby je mohl dotaz zahrnout do výsledků. Položka JSON musí vyhodnotit zadané podmínky, aby se pro výsledek považovaly za `true`. Vrstva indexu používá klauzuli WHERE k určení nejmenší podmnožiny zdrojových položek, které mohou být součástí výsledku.
  
## <a name="syntax"></a>Syntaxe
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumenty

- `<filter_condition>`  
  
   Určuje podmínku, která má být splněna pro vrácení dokumentů.  
  
- `<scalar_expression>`  
  
   Výraz představující hodnotu, která má být vypočítána. Podrobnosti najdete v tématu [skalární výrazy](sql-query-scalar-expressions.md) .  
  

## <a name="remarks"></a>Poznámky
  
  Aby bylo možné dokument vrátit, je nutné vyhodnotit výraz zadaný jako podmínka filtru na hodnotu true. Pouze logická hodnota true bude splnit podmínku, jakákoli jiná hodnota: undefined, null, false, Number, Array nebo Object nesplňuje podmínku. 

## <a name="examples"></a>Příklady

Následující dotaz požádá o položky, které obsahují vlastnost `id`, jejíž hodnota je `AndersenFamily`. Vyloučí žádnou položku, která nemá vlastnost `id` nebo jejíž hodnota neodpovídá `AndersenFamily`.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Skalární výrazy v klauzuli WHERE

Předchozí příklad ukázal jednoduchý dotaz rovnosti. Rozhraní SQL API podporuje také různé [skalární výrazy](sql-query-scalar-expressions.md). Nejběžněji používané jsou binární a unární výrazy. Odkazy na vlastnosti ze zdrojového objektu JSON jsou také platné výrazy.

Můžete použít následující podporované binární operátory:  

|**Typ operátoru**  | **Hodnoty** |
|---------|---------|
|Průměr | +,-,*,/,% |
|Číslo2    | \|, &, ^, < <, > >, > > > (s nulovou výplní vpravo Shift) |
|Biolog    | A, NEBO, NOT      |
|Porovnání | =,! =, &lt;, &gt;, &lt; =, &gt; =, < > |
|Řetězec     |  \| @ no__t-1 (zřetězení) |

Následující dotazy používají binární operátory:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Můžete také použít unární operátory +,-, ~ a ne v dotazech, jak je znázorněno v následujících příkladech:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Odkazy na vlastnosti můžete použít také v dotazech. Například `SELECT * FROM Families f WHERE f.isRegistered` vrátí položku JSON obsahující Vlastnost `isRegistered` s hodnotou rovnou `true`. Jakákoli jiná hodnota, například `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>` nebo `<array>`, vyloučí položku z výsledku. 

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzule FROM](sql-query-from.md)