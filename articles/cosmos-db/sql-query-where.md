---
title: Klauzule WHERE v Azure Cosmos DB
description: Seznamte se s klauzulí WHERE SQL pro Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898786"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Klauzule WHERE v Azure Cosmos DB

Volitelná klauzule WHERE (`WHERE <filter_condition>`) určuje podmínky, které musí zdrojové položky JSON splňovat, aby se dotaz zahrnul do výsledků. Položka JSON musí vyhodnotit zadané podmínky, které se mají pro výsledek považovat za `true`. Vrstva indexu používá klauzuli WHERE k určení nejmenší podmnožiny zdrojových položek, které mohou být součástí výsledku.
  
## <a name="syntax"></a>Syntaxe
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumenty

- `<filter_condition>`  
  
   Určuje podmínku, která má být splněny pro dokumenty, které se mají vrátit.  
  
- `<scalar_expression>`  
  
   Výraz představující hodnotu, která chcete vypočítat. Podrobnosti najdete v tématu [skalární výrazy](sql-query-scalar-expressions.md) .  
  
## <a name="remarks"></a>Poznámky
  
  Aby dokumentu, který má být vrácen výraz zadaný jako filtr musí podmínka vyhodnocena na hodnotu true. Pouze logická hodnota `true` splní podmínku, jakákoli jiná hodnota: undefined, null, false, Number, Array nebo Object nesplňuje podmínky.

  Pokud zahrnete klíč oddílu do klauzule `WHERE` jako součást filtru rovnosti, dotaz se automaticky vyfiltruje jenom na relevantní oddíly.

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

Předchozí příklad ukázal dotaz rovnosti jednoduché. Rozhraní SQL API podporuje také různé [skalární výrazy](sql-query-scalar-expressions.md). Nejčastěji používané jsou binární soubor a unární výrazy. Odkazy na vlastnosti z objektu JSON zdroje jsou také výrazy platný.

Můžete použít následující podporované binární operátory:  

|**Typ operátoru**  | **Hodnoty** |
|---------|---------|
|Aritmetické operace | +,-,*,/,% |
|bitové operace    | \|, &, ^, < <, > >, > > > (0 – vyplnit vpravo Shift) |
|Logické    | A, NEBO NE      |
|Porovnání | =,! =, &lt;, &gt;, &lt;=, &gt;=, < > |
|String     |  \|\| (zřetězení) |

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

Odkazy na vlastnosti můžete použít také v dotazech. Například `SELECT * FROM Families f WHERE f.isRegistered` vrátí položku JSON obsahující Vlastnost `isRegistered` s hodnotou rovnou `true`. Jakákoli jiná hodnota, například `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`nebo `<array>`, vyloučí položku z výsledku.

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Klíčové slovo IN](sql-query-keywords.md#in)
- [Klauzule FROM](sql-query-from.md)