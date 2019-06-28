---
title: SELECT – klauzule ve službě Azure Cosmos DB
description: Další informace o SQL klauzule SELECT pro službu Azure Cosmos DB. Použití SQL jako dotazovací jazyk Azure Cosmos DB JSON.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 84d0212f7f212b4554b506726e027fe51f795eea
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342694"
---
# <a name="select-clause"></a>Klauzule SELECT

Každý dotaz obsahuje klauzuli SELECT a volitelné [FROM](sql-query-from.md) a [kde](sql-query-where.md) klauzule podle standardů ANSI SQL. Obvykle je vypočten zdroji v klauzuli FROM a klauzuli WHERE aplikuje filtr na zdroj k načtení podmnožiny položky formátu JSON. Klauzule SELECT a projekty požadované hodnoty JSON v seznamu select.

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
  
  Určuje, zda by měly být odstraněny duplicity projektovaných vlastností.  

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

Vyberte následující dotaz vrací hodnotu příklad `address` z `Families` jehož `id` odpovídá `AndersenFamily`:

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

### <a name="quoted-property-accessor"></a>Přistupující objekt vlastnosti v uvozovkách
Můžete přistupovat k vlastnosti pomocí vlastností v uvozovkách operator []. Například `SELECT c.grade` a `SELECT c["grade"]` jsou ekvivalentní. Tato syntaxe je užitečné k uvození vlastnost, která obsahuje mezery, speciální znaky, nebo má stejný název jako klíčové slovo SQL nebo vyhrazené slovo.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Vnořené vlastnosti

Následující příklad projekty dvě vnořené vlastnosti `f.address.state` a `f.address.city`.

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

V předchozím příkladu klauzule SELECT potřebuje k vytvoření objektu JSON, a protože ukázka poskytuje žádný klíč, v klauzuli používá název proměnné implicitní argument `$1`. Následující dotaz vrátí dvě proměnné implicitní argument: `$1` a `$2`.

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
- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Klauzule WHERE](sql-query-where.md)