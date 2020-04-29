---
title: Klauzule SELECT v Azure Cosmos DB
description: Seznamte se s klauzulí SELECT jazyka SQL pro Azure Cosmos DB. Použijte SQL jako dotazovací jazyk Azure Cosmos DB JSON.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
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

  Vlastnosti nebo hodnoty, které mají být vybrány pro sadu výsledků.  
  
- `'*'`  

  Určuje, zda má být hodnota načtena bez provedení změn. Konkrétně Pokud je zpracována hodnota objektu, budou načteny všechny vlastnosti.  
  
- `<object_property_list>`  
  
  Určuje seznam vlastností, které mají být načteny. Každá vrácená hodnota bude objekt se zadanými vlastnostmi.  
  
- `VALUE`  

  Určuje, že se má místo úplného objektu JSON načíst hodnota JSON. To, na rozdíl `<property_list>` od, nezalomí předpokládané hodnoty v objektu.  
 
- `DISTINCT`
  
  Určuje, že se mají odebrat duplicity vlastností projektu.  

- `<scalar_expression>`  

  Výraz představující hodnotu, která má být vypočítána. Podrobnosti najdete v části [skalární výrazy](sql-query-scalar-expressions.md) .  

## <a name="remarks"></a>Poznámky

`SELECT *` Syntaxe je platná pouze v případě, že klauzule FROM deklaruje přesně jeden alias. `SELECT *`poskytuje projekci identity, která může být užitečná, pokud není potřeba žádná projekce. PŘÍKAZ SELECT * je platný pouze v případě, že je zadána klauzule FROM a byl zaveden pouze jeden vstupní zdroj.  
  
`SELECT <select_list>` A `SELECT *` jsou "syntaktický cukr" a mohou být případně vyjádřeny pomocí jednoduchých příkazů SELECT, jak je znázorněno níže.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   je ekvivalentem:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   je ekvivalentem:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Příklady

Následující příklad dotazu SELECT se vrátí `address` z `Families` jeho `id` shody `AndersenFamily`:

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

Výsledky jsou následující:

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

Výsledky jsou následující:

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

Výsledky jsou následující:

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

Pokud vaše data obsahují vlastnosti se stejnými názvy jako vyhrazená klíčová slova, jako je "objednávka" nebo "skupina", dotazy na tyto dokumenty budou mít za následek chyby syntaxe. K úspěšnému spuštění dotazu byste měli `[]` explicitně zahrnout vlastnost ve znaku.

Například zde je dokument s vlastností s názvem `order` a vlastností `price($)` , která obsahuje speciální znaky:

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

Pokud spustíte dotazy, které obsahují `order` vlastnost nebo `price($)` vlastnost, dojde k chybě syntaxe.

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
