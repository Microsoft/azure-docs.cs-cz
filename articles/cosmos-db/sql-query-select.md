---
title: Klauzule SELECT v Azure Cosmos DB
description: Seznamte se s klauzulí SELECT jazyka SQL pro Azure Cosmos DB. Použijte SQL jako dotazovací jazyk Azure Cosmos DB JSON.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 072e17b1c0ea312b4adfa1687e447fd2cadde233
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335432"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Klauzule SELECT v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Každý dotaz se skládá z `SELECT` klauzule a volitelných klauzulí [from](sql-query-from.md) a [WHERE](sql-query-where.md) podle standardů ANSI SQL. Obvykle je zdroj v klauzuli vyhodnocen `FROM` a `WHERE` klauzule používá filtr na zdroj pro načtení podmnožiny položek JSON. `SELECT`Klauzule potom v seznamu Select vypíše požadované hodnoty JSON.

## <a name="syntax"></a>Syntaxe

```sql
SELECT <select_specification>  

<select_specification> ::=
      '*'
      | [DISTINCT] <object_property_list>
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
```  
  
## <a name="arguments"></a>Argumenty
  
- `<select_specification>`  

  Vlastnosti nebo hodnoty, které mají být vybrány pro sadu výsledků.  
  
- `'*'`  

  Určuje, zda má být hodnota načtena bez provedení změn. Konkrétně Pokud je zpracována hodnota objektu, budou načteny všechny vlastnosti.  
  
- `<object_property_list>`  
  
  Určuje seznam vlastností, které mají být načteny. Každá vrácená hodnota bude objekt se zadanými vlastnostmi.  
  
- `VALUE`  

  Určuje, že se má místo úplného objektu JSON načíst hodnota JSON. To, na rozdíl od, `<property_list>` nezalomí předpokládané hodnoty v objektu.  

- `DISTINCT`
  
  Určuje, že se mají odebrat duplicity vlastností projektu.  

- `<scalar_expression>`  

  Výraz představující hodnotu, která má být vypočítána. Podrobnosti najdete v části [skalární výrazy](sql-query-scalar-expressions.md) .  

## <a name="remarks"></a>Poznámky

`SELECT *`Syntaxe je platná pouze v případě, že klauzule FROM deklaruje přesně jeden alias. `SELECT *` poskytuje projekci identity, která může být užitečná, pokud není potřeba žádná projekce. PŘÍKAZ SELECT * je platný pouze v případě, že je zadána klauzule FROM a byl zaveden pouze jeden vstupní zdroj.  
  
`SELECT <select_list>`A `SELECT *` jsou "syntaktický cukr" a mohou být případně vyjádřeny pomocí jednoduchých příkazů SELECT, jak je znázorněno níže.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   je ekvivalentem:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   je ekvivalentem:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Příklady

Následující příklad dotazu SELECT se vrátí `address` z `Families` jeho `id` shody `AndersenFamily` :

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

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzule WHERE](sql-query-where.md)
