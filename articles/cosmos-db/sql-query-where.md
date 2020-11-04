---
title: Klauzule WHERE v Azure Cosmos DB
description: Seznamte se s klauzulí WHERE SQL pro Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 5620a9fb95fb52a487095afd75d5f30c82a8bce1
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341463"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Klauzule WHERE v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Volitelná klauzule WHERE ( `WHERE <filter_condition>` ) určuje podmínky, které musí zdrojové položky JSON splňovat, aby se dotaz zahrnul do výsledků. Položka JSON musí vyhodnotit zadané podmínky, které mají `true` být pro výsledek zváženy. Vrstva indexu používá klauzuli WHERE k určení nejmenší podmnožiny zdrojových položek, které mohou být součástí výsledku.
  
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

  Pokud zahrnete klíč oddílu do klauzule v `WHERE` rámci filtru rovnosti, dotaz se automaticky vyfiltruje jenom na relevantní oddíly.

## <a name="examples"></a>Příklady

Následující dotaz vyžádá položky, které obsahují `id` vlastnost, jejíž hodnota je `AndersenFamily` . Vyloučí žádnou položku, která nemá `id` vlastnost nebo jejíž hodnota neodpovídá `AndersenFamily` .

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
|Aritmetické | +,-,*,/,% |
|Bitové    | \|, &, ^,  <<,  >>  >>>  (s nulovou výplní vpravo Shift) |
|Logické    | A, NEBO, NOT      |
|Porovnání | =,! =, &lt; , &gt; , &lt; =, &gt; =,  <> |
|Řetězec     |  \|\| zřetězit |

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

Odkazy na vlastnosti můžete použít také v dotazech. Například `SELECT * FROM Families f WHERE f.isRegistered` vrátí položku JSON obsahující Vlastnost `isRegistered` s hodnotou rovnou `true` . Jakákoli jiná hodnota, například,,, `false` `null` `Undefined` `<number>` , `<string>` , `<object>` , nebo `<array>` , vyloučí položku z výsledku. Kromě toho můžete použít `IS_DEFINED` funkci pro kontrolu typů k dotazování na základě přítomnosti nebo absence dané vlastnosti JSON. Například `SELECT * FROM Families f WHERE NOT IS_DEFINED(f.isRegistered)` vrátí libovolnou položku JSON, která nemá hodnotu pro `isRegistered` .

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Klíčové slovo IN](sql-query-keywords.md#in)
- [Klauzule FROM](sql-query-from.md)
