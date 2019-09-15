---
title: Klauzule SELECT v Azure Cosmos DB
description: Seznamte se s klauzulí SELECT jazyka SQL pro Azure Cosmos DB. Použijte SQL jako dotazovací jazyk Azure Cosmos DB JSON.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: d34b1c39d9789409dc365cd4cf07fdc3d5a780fd
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003516"
---
# <a name="select-clause"></a>Klauzule SELECT

Každý dotaz se skládá z klauzule SELECT a volitelné klauzule [from](sql-query-from.md) a [WHERE](sql-query-where.md) podle standardů ANSI SQL. Obvykle je uveden výčet zdroje v klauzuli FROM a klauzule WHERE aplikuje filtr na zdroj, aby získal podmnožinu položek JSON. Klauzule SELECT potom v seznamu Select vyžádá projekty požadované hodnoty JSON.

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

  Vlastnosti nebo hodnota, která má být vybrána sada výsledků.  
  
- `'*'`  

  Určuje, že mají být načtena hodnota beze změn. Konkrétně, pokud je zpracovaných hodnota objekt, budou načítat všechny vlastnosti.  
  
- `<object_property_list>`  
  
  Určuje seznam vlastností, které se mají načíst. Každá vrácená hodnota bude objekt s vlastnostmi zadán.  
  
- `VALUE`  

  Určuje, že hodnota JSON má být načtena namísto kompletního objektu JSON. To, na rozdíl od `<property_list>` nezalamuje očekávaná hodnota do objektu.  
 
- `DISTINCT`
  
  Určuje, že se mají odebrat duplicity vlastností projektu.  

- `<scalar_expression>`  

  Výraz představující hodnotu, která chcete vypočítat. Zobrazit [skalární výrazy](sql-query-scalar-expressions.md) podrobné informace.  

## <a name="remarks"></a>Poznámky

`SELECT *` Syntaxe je platná pouze pokud klauzule FROM deklaroval přesně jeden alias. `SELECT *` poskytuje identitu projekce, které mohou být užitečné v případě potřeby žádná projekce. Vyberte * je platná pouze pokud je zadaná klauzule FROM a zavedl pouze jednoho vstupního zdroje.  
  
Obě `SELECT <select_list>` a `SELECT *` jsou "syntaktické sugar" a může být také vyjádřena pomocí jednoduchých příkazů SELECT, jak je znázorněno níže.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   je ekvivalentní:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   je ekvivalentní:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Příklady

Následující příklad dotazu Select se vrátí `address` `id` z `Families` jeho shody `AndersenFamily`:

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

### <a name="quoted-property-accessor"></a>Přístup k vlastnostem v uvozovkách
K vlastnostem můžete přistupovat pomocí operátoru vlastnosti v uvozovkách []. Například `SELECT c.grade` a `SELECT c["grade"]` jsou ekvivalentní. Tato syntaxe je užitečná pro řídicí znak, který obsahuje mezery, speciální znaky nebo má stejný název jako klíčové slovo SQL nebo vyhrazené slovo.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Vnořené vlastnosti

Následující příklad projekty jsou dvě vnořené vlastnosti `f.address.state` a. `f.address.city`

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### <a name="json-expressions"></a>Výrazy JSON

Projekce také podporuje výrazy JSON, jak je znázorněno v následujícím příkladu:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

V předchozím příkladu musí klauzule SELECT vytvořit objekt JSON a protože ukázka neposkytuje žádný klíč, klauzule používá název `$1`proměnné implicitního argumentu. Následující dotaz vrátí dvě proměnné implicitního argumentu `$1` : `$2`a.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Další postup

- [Začínáme](sql-query-getting-started.md)
- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzule WHERE](sql-query-where.md)