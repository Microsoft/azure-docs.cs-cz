---
title: Klauzule GROUP BY v Azure Cosmos DB
description: Seznamte se s klauzulí GROUP BY pro Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: tisande
ms.openlocfilehash: 8a3cbbafc066747b62f79934f2cd12301aa1ba17
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261597"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Klauzule GROUP BY v Azure Cosmos DB

Klauzule GROUP BY rozděluje výsledky dotazu podle hodnot jedné nebo více zadaných vlastností.

> [!NOTE]
> Azure Cosmos DB aktuálně podporuje GROUP BY v sadě .NET SDK 3,3 a vyšší a také v sadě JavaScript SDK 3,4 a vyšší.
> Podpora pro jinou jazykovou sadu SDK není aktuálně k dispozici, ale je plánována.

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
  
  Pokud dotaz používá klauzuli GROUP BY, klauzule SELECT může obsahovat pouze podmnožinu vlastností a systémových funkcí obsažených v klauzuli GROUP BY. Jedna výjimka je [agregovaná systémová funkce](sql-query-aggregates.md), která se může objevit v klauzuli SELECT bez zahrnutí v klauzuli Group by. Do klauzule SELECT můžete také vždy zahrnout hodnoty literálu.

  Klauzule GROUP BY musí být za klauzulí SELECT, FROM a WHERE a před klauzulí LIMITu POSUNu. V tuto chvíli nemůžete použít klauzuli GROUP BY s klauzulí ORDER BY, ale to je plánováno.

  Klauzule GROUP BY nepovoluje žádnou z následujících možností:
  
- Vlastnosti aliasů nebo funkce systému vytváření aliasů (aliasy jsou pořád povolené v rámci klauzule SELECT)
- Poddotazy
- Agregační systémové funkce (jsou povolené jenom v klauzuli SELECT)

Dotazy s agregovanou systémovou funkcí a poddotazem `GROUP BY` s nejsou podporovány. Například následující dotaz není podporován:

```sql
SELECT COUNT(UniqueLastNames) FROM (SELECT AVG(f.age) FROM f GROUP BY f.lastName) AS UniqueLastNames
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

Tento dotaz má dva výrazy, které slouží k rozdělení výsledků:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Mezi výsledky patří:

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

Tento dotaz obsahuje systémovou funkci v klauzuli GROUP BY:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Mezi výsledky patří:

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

Tento dotaz ve výrazu vlastnosti Item používá jak klíčová slova, tak systémové funkce:

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
