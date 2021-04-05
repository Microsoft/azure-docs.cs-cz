---
title: Klauzule GROUP BY v Azure Cosmos DB
description: Seznamte se s klauzulí GROUP BY pro Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: d9cafc100ddd4b553577c447e82334e6ee7d1b6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96545413"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Klauzule GROUP BY v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Klauzule GROUP BY rozděluje výsledky dotazu podle hodnot jedné nebo více zadaných vlastností.

## <a name="syntax"></a>Syntaxe

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argumenty

- `<scalar_expression_list>`

   Určuje výrazy, které budou použity k rozdělení výsledků dotazu.

- `<scalar_expression>`
  
   Jakýkoli skalární výraz je povolen s výjimkou skalárních poddotazů a skalárních agregací. Každý skalární výraz musí obsahovat alespoň jeden odkaz na vlastnost. Neexistuje žádné omezení počtu jednotlivých výrazů nebo mohutnosti každého výrazu.

## <a name="remarks"></a>Poznámky
  
  Pokud dotaz používá klauzuli GROUP BY, klauzule SELECT může obsahovat pouze podmnožinu vlastností a systémových funkcí obsažených v klauzuli GROUP BY. Jedinou výjimkou jsou [agregační funkce](sql-query-aggregate-functions.md), které se mohou objevit v klauzuli SELECT bez zahrnutí v klauzuli Group by. Do klauzule SELECT můžete také vždy zahrnout hodnoty literálu.

  Klauzule GROUP BY musí být za klauzulí SELECT, FROM a WHERE a před klauzulí LIMITu POSUNu. V tuto chvíli nemůžete použít klauzuli GROUP BY s klauzulí ORDER BY, ale to je plánováno.

  Klauzule GROUP BY nepovoluje žádnou z následujících možností:
  
- Vlastnosti aliasů nebo funkce systému vytváření aliasů (aliasy jsou pořád povolené v rámci klauzule SELECT)
- Poddotazy
- Agregační systémové funkce (jsou povolené jenom v klauzuli SELECT)

Dotazy s agregovanou systémovou funkcí a poddotazem s `GROUP BY` nejsou podporovány. Například následující dotaz není podporován:

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

## <a name="examples"></a>Příklady

Tyto příklady používají sadu nutričních dat, která je dostupná prostřednictvím [Azure Cosmos DB testovací prostředí dotazů](https://www.documentdb.com/sql/demo).

Tady je například dotaz, který vrátí celkový počet položek v každé ze všech jídel:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Některé výsledky jsou (klíčové slovo TOP slouží k omezení výsledků):

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta"
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products"
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes"
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs"
    }
]
```

Tento dotaz má dva výrazy, které slouží k rozdělení výsledků:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Mezi výsledky patří:

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta",
        "version": 1
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products",
        "version": 1
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes",
        "version": 1
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs",
        "version": 1
    }
]
```

Tento dotaz obsahuje systémovou funkci v klauzuli GROUP BY:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Mezi výsledky patří:

```json
[
    {
        "foodGroupCount": 183,
        "upperFoodGroup": "CEREAL GRAINS AND PASTA"
    },
    {
        "foodGroupCount": 133,
        "upperFoodGroup": "NUT AND SEED PRODUCTS"
    },
    {
        "foodGroupCount": 113,
        "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
    },
    {
        "foodGroupCount": 64,
        "upperFoodGroup": "SPICES AND HERBS"
    }
]
```

Tento dotaz ve výrazu vlastnosti Item používá jak klíčová slova, tak systémové funkce:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Výsledky jsou následující:

```json
[
    {
        "foodGroupCount": 10,
        "containsOrangeTag": true,
        "correctVersion": true
    },
    {
        "foodGroupCount": 8608,
        "containsOrangeTag": false,
        "correctVersion": true
    }
]
```

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Klauzule SELECT](sql-query-select.md)
- [Agregační funkce](sql-query-aggregate-functions.md)
