---
title: Klauzule WHERE ve službě Azure Cosmos DB
description: Další informace o klauzule WHERE příkazu SQL pro službu Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 6a942e48ffea7785fe971cc2f8fa66e8569ed672
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342475"
---
# <a name="where-clause"></a>Klauzule WHERE

Volitelná klauzule WHERE (`WHERE <filter_condition>`) určuje podmínky, že zdrojové položky formátu JSON musí splňovat pro dotaz můžete zahrnout do výsledků. Položka formátu JSON musí být vyhodnocen zadané podmínky, které mají `true` považovat pro výsledek. Index vrstvy používá klauzuli WHERE určit nejmenší podmnožinu zdrojové položky, které můžou být součástí výsledku.
  
## <a name="syntax"></a>Syntaxe
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumenty

- `<filter_condition>`  
  
   Určuje podmínku, která má být splněny pro dokumenty, které se mají vrátit.  
  
- `<scalar_expression>`  
  
   Výraz představující hodnotu, která chcete vypočítat. Zobrazit [skalární výrazy](sql-query-scalar-expressions.md) podrobnosti.  
  

## <a name="remarks"></a>Poznámky
  
  Aby dokumentu, který má být vrácen výraz zadaný jako filtr musí podmínka vyhodnocena na hodnotu true. Pouze pro logickou hodnotu true budou splňují zadanou podmínku, jakákoli jiná hodnota: undefined, null, hodnotu false, číslo, pole nebo objekt nebude splňují zadanou podmínku. 

## <a name="examples"></a>Příklady

Následující dotaz položky požadavků, které obsahují `id` vlastnost, jejíž hodnota je `AndersenFamily`. Vyloučí všechny položky, které nemá `id` vlastnost nebo jejichž hodnota neodpovídá `AndersenFamily`.

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

Předchozí příklad ukázal dotaz rovnosti jednoduché. Rozhraní SQL API také podporuje různé [skalární výrazy](sql-query-scalar-expressions.md). Nejčastěji používané jsou binární soubor a unární výrazy. Odkazy na vlastnosti z objektu JSON zdroje jsou také výrazy platný.

Můžete použít tyto podporované binární operátory:  

|**Typ operátoru**  | **Hodnoty** |
|---------|---------|
|Aritmetické operace | +,-,*,/,% |
|bitové operace    | \|, &, ^, <<>>,, >>> (výplně nulové posunutí doprava) |
|Logické    | A, NEBO NE      |
|Porovnání | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Řetězec     |  \|\| (zřetězení) |

Binární operátory použít následující dotazy:

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

Můžete také použít unární operátory +,-, ~ a ne v dotazech, jak je znázorněno v následujícím příkladu:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Odkazy na vlastnosti můžete použít taky v dotazech. Například `SELECT * FROM Families f WHERE f.isRegistered` vrátí položku JSON obsahující vlastnost `isRegistered` s hodnotou rovná `true`. Jakékoli jiné hodnoty, například `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, nebo `<array>`, vyloučí položky z výsledku. 

## <a name="next-steps"></a>Další postup

- [Začínáme](sql-query-getting-started.md)
- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [FROM – klauzule](sql-query-from.md)