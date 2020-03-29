---
title: Klauzule GROUP BY v Db Služby Azure Cosmos
description: Další informace o klauzuli GROUP BY pro Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tisande
ms.openlocfilehash: e41e81457421bfe27e3c0313fc06e39e6df4cdce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73819106"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Klauzule GROUP BY v Db Služby Azure Cosmos

Klauzule GROUP BY rozděluje výsledky dotazu podle hodnot jedné nebo více zadaných vlastností.

> [!NOTE]
> Azure Cosmos DB aktuálně podporuje GROUP BY v .NET SDK 3.3 a vyšší, stejně jako JavaScript SDK 3.4 a vyšší.
> Podpora pro jiné jazyky sady SDK není v současné době k dispozici, ale je plánována.

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
  
   Každý skalární výraz je povolen s výjimkou skalárních poddotazů a skalárních agregací. Každý skalární výraz musí obsahovat alespoň jeden odkaz na vlastnost. Neexistuje žádné omezení počtu jednotlivých výrazů nebo mohutnost každého výrazu.

## <a name="remarks"></a>Poznámky
  
  Pokud dotaz používá klauzuli GROUP BY, klauzule SELECT může obsahovat pouze podmnožinu vlastností a systémových funkcí zahrnutých v klauzuli GROUP BY. Jednou výjimkou jsou [agregační systémové funkce](sql-query-aggregates.md), které se mohou objevit v klauzuli SELECT, aniž by byly zahrnuty do klauzule GROUP BY. Do klauzule SELECT můžete také vždy zahrnout hodnoty literálu.

  Klauzule GROUP BY musí být za klauzulí SELECT, FROM a WHERE a před klauzulí OFFSET LIMIT. V současné době nelze použít GROUP BY s klauzulí ORDER BY, ale je plánováno.

  Klauzule GROUP BY neumožňuje žádné z následujících položek:
  
- Vlastnosti aliasingu nebo systémové funkce aliasingu (aliasing je stále povolen v klauzuli SELECT)
- Poddotazů
- Agregační systémové funkce (ty jsou povoleny pouze v klauzuli SELECT)

## <a name="examples"></a>Příklady

Tyto příklady používají sadu dat o výživě, která je k dispozici prostřednictvím [hřiště pro dotazy Azure Cosmos DB .](https://www.documentdb.com/sql/demo)

Například zde je dotaz, který vrací celkový počet položek v každé foodGroup:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Některé výsledky jsou (TOP klíčové slovo se používá k omezení výsledků):

```json
[{
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "foodGroup": "Meals, Entrees, and Side Dishes",
  "foodGroupCount": 113
},
{
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Tento dotaz má dva výrazy používané k rozdělení výsledků:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Některé výsledky jsou:

```json
[{
  "version": 1,
  "foodGroup": "Nut and Seed Products",
  "foodGroupCount": 133
},
{
  "version": 1,
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "version": 1,
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "version": 1,
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Tento dotaz má systémovou funkci v klauzuli GROUP BY:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Některé výsledky jsou:

```json
[{
  "foodGroupCount": 371,
  "upperFoodGroup": "FAST FOODS"
},
{
  "foodGroupCount": 267,
  "upperFoodGroup": "FINFISH AND SHELLFISH PRODUCTS"
},
{
  "foodGroupCount": 389,
  "upperFoodGroup": "LEGUMES AND LEGUME PRODUCTS"
},
{
  "foodGroupCount": 113,
  "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
}]
```

Tento dotaz používá klíčová slova i systémové funkce ve výrazu vlastnosti položky:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Výsledky jsou následující:

```json
[{
  "correctVersion": true,
  "containsOrangeTag": false,
  "foodGroupCount": 8608
},
{
  "correctVersion": true,
  "containsOrangeTag": true,
  "foodGroupCount": 10
}]
```

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Klauzule SELECT](sql-query-select.md)
- [Agregační funkce](sql-query-aggregates.md)
