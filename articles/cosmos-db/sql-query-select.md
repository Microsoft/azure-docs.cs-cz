---
title: Klauzule SELECT v Azure Cosmos DB
description: Seznamte se s klauzulí SELECT jazyka SQL pro Azure Cosmos DB. Použijte SQL jako dotazovací jazyk Azure Cosmos DB JSON.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77469931"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Klauzule SELECT v Azure Cosmos DB

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

  Určuje, že hodnota JSON má být načtena namísto kompletního objektu JSON. To na rozdíl od `<property_list>` nezalomí předpokládané hodnoty v objektu.  
 
- `DISTINCT`
  
  Určuje, že se mají odebrat duplicity vlastností projektu.  

- `<scalar_expression>`  

  Výraz představující hodnotu, která chcete vypočítat. Podrobnosti najdete v části [skalární výrazy](sql-query-scalar-expressions.md) .  

## <a name="remarks"></a>Poznámky

Syntaxe `SELECT *` je platná pouze tehdy, když klauzule FROM deklaruje přesně jeden alias. `SELECT *` poskytuje projekci identity, která může být užitečná, pokud není potřeba žádná projekce. Vyberte * je platná pouze pokud je zadaná klauzule FROM a zavedl pouze jednoho vstupního zdroje.  
  
`SELECT <select_list>` i `SELECT *` jsou "syntaktický cukr" a mohou být případně vyjádřeny pomocí jednoduchých příkazů SELECT, jak je znázorněno níže.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   je ekvivalentní:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   je ekvivalentní:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Příklady

Následující příklad dotazu SELECT vrátí `address` z `Families`, jejichž `id` odpovídá `AndersenFamily`:

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

Následující příklad projektuje dvě vnořené vlastnosti `f.address.state` a `f.address.city`.

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

V předchozím příkladu je nutné, aby klauzule SELECT vytvořila objekt JSON a protože ukázka neposkytuje žádný klíč, klauzule používá implicitní název proměnné argumentu `$1`. Následující dotaz vrátí dvě proměnné implicitního argumentu: `$1` a `$2`.

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
## <a name="reserved-keywords-and-special-characters"></a>Vyhrazená klíčová slova a speciální znaky

Pokud vaše data obsahují vlastnosti se stejnými názvy jako vyhrazená klíčová slova, jako je "objednávka" nebo "skupina", dotazy na tyto dokumenty budou mít za následek chyby syntaxe. K úspěšnému spuštění dotazu byste měli explicitně zahrnout vlastnost `[]`ho znaku.

Například zde je dokument s vlastností s názvem `order` a `price($)` vlastností, která obsahuje speciální znaky:

```json
{
  "id": "AndersenFamily",
  "order": [
     {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
     }
  ],
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Pokud spustíte dotazy, které zahrnují vlastnost `order` nebo vlastnost `price($)`, zobrazí se chyba syntaxe.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```
```sql
SELECT * FROM c where c.order.price($) > 50
```
Výsledek je následující:

`
Syntax error, incorrect syntax near 'order'
`

Stejné dotazy byste měli přepsat následujícím způsobem:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzule WHERE](sql-query-where.md)
