---
title: Klauzule WHERE v Azure Cosmos DB
description: Seznamte se s klauzulí WHERE SQL pro Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 362024868de269ed64a440a25e8c19c5b68bef80
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003461"
---
# <a name="where-clause"></a>Klauzule WHERE

Volitelná klauzule WHERE (`WHERE <filter_condition>`) určuje podmínky, které musí zdrojové položky JSON splňovat, aby se dotaz zahrnul do výsledků. Položka JSON musí vyhodnotit zadané podmínky, `true` které mají být pro výsledek zváženy. Vrstva indexu používá klauzuli WHERE k určení nejmenší podmnožiny zdrojových položek, které mohou být součástí výsledku.
  
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
  
  Aby dokumentu, který má být vrácen výraz zadaný jako filtr musí podmínka vyhodnocena na hodnotu true. Pouze pro logickou hodnotu true budou splňují zadanou podmínku, jakákoli jiná hodnota: undefined, null, hodnotu false, číslo, pole nebo objekt nebude splňují zadanou podmínku. 

## <a name="examples"></a>Příklady

Následující dotaz vyžádá položky, které obsahují `id` vlastnost, jejíž hodnota `AndersenFamily`je. Vyloučí žádnou položku, která `id` nemá vlastnost nebo jejíž hodnota neodpovídá. `AndersenFamily`

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
|bitové operace    | \|, &, ^, <<>>,, >>> (výplně nulové posunutí doprava) |
|Logické    | A, NEBO NE      |
|Porovnání | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Řetězec     |  \|\| (zřetězení) |

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

Odkazy na vlastnosti můžete použít také v dotazech. Například `SELECT * FROM Families f WHERE f.isRegistered` vrátí položku JSON obsahující Vlastnost `isRegistered` s hodnotou rovnou `true`. Jakákoli `false`jiná hodnota, například `null` `Undefined` `<string>`,, ,`<object>`,,, nebo`<array>`, vyloučí položku z výsledku. `<number>` 

## <a name="next-steps"></a>Další postup

- [Začínáme](sql-query-getting-started.md)
- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [FROM – klauzule](sql-query-from.md)