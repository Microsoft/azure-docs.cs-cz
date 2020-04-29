---
title: Klauzule WHERE v Azure Cosmos DB
description: Seznamte se s klauzulí WHERE SQL pro Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898786"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Klauzule WHERE v Azure Cosmos DB

Volitelná klauzule WHERE (`WHERE <filter_condition>`) určuje podmínky, které musí zdrojové položky JSON splňovat, aby se dotaz zahrnul do výsledků. Položka JSON musí vyhodnotit zadané podmínky, `true` které mají být pro výsledek zváženy. Vrstva indexu používá klauzuli WHERE k určení nejmenší podmnožiny zdrojových položek, které mohou být součástí výsledku.
  
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
  
  Aby bylo možné dokument vrátit, je nutné vyhodnotit výraz zadaný jako podmínka filtru na hodnotu true. Pouze logická hodnota `true` bude vyhovovat podmínkám, jakákoli jiná hodnota: undefined, null, false, Number, Array nebo Object nesplňuje podmínky.

  Pokud zahrnete klíč oddílu do `WHERE` klauzule v rámci filtru rovnosti, dotaz se automaticky vyfiltruje jenom na relevantní oddíly.

## <a name="examples"></a>Příklady

Následující dotaz vyžádá položky, které obsahují `id` vlastnost, jejíž hodnota `AndersenFamily`je. Vyloučí žádnou položku, která nemá `id` vlastnost nebo jejíž hodnota neodpovídá. `AndersenFamily`

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou následující:

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
|Číslo2    | \|, &, ^,  <<,  >>  >>>  (s nulovou výplní vpravo Shift) |
|Logické    | A, NEBO, NOT      |
|Srovnání | =,! =, &lt;, &gt;, &lt;=, &gt;=,  <> |
|Řetězec     |  \|\|zřetězit |

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

Odkazy na vlastnosti můžete použít také v dotazech. Například `SELECT * FROM Families f WHERE f.isRegistered` vrátí položku JSON obsahující Vlastnost `isRegistered` s hodnotou rovnou. `true` Jakákoli jiná hodnota `false`, například `null` `Undefined`,,, `<number>`, `<string>`, `<object>`, nebo `<array>`, vyloučí položku z výsledku.

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Klíčové slovo IN](sql-query-keywords.md#in)
- [Klauzule FROM](sql-query-from.md)