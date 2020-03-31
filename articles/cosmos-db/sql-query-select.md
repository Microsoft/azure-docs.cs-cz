---
title: Klauzule SELECT v Azure Cosmos DB
description: Další informace o klauzuli SQL SELECT pro Azure Cosmos DB. Použijte SQL jako dotazovací jazyk Azure Cosmos DB JSON.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469931"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Klauzule SELECT v Azure Cosmos DB

Každý dotaz se skládá z klauzule SELECT a volitelné [from](sql-query-from.md) a [where](sql-query-where.md) klauzule, podle standardů ANSI SQL. Obvykle je uveden zdroj v klauzuli FROM a klauzule WHERE použije filtr na zdroj k načtení podmnožiny položek JSON. Klauzule SELECT pak promítne požadované hodnoty JSON do seznamu výběru.

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

  Vlastnosti nebo hodnota, které mají být vybrány pro sadu výsledků.  
  
- `'*'`  

  Určuje, že hodnota by měla být načtena bez provedení jakýchkoli změn. Konkrétně pokud je zpracovaná hodnota objektem, budou načteny všechny vlastnosti.  
  
- `<object_property_list>`  
  
  Určuje seznam vlastností, které mají být načteny. Každá vrácená hodnota bude objekt se zadanými vlastnostmi.  
  
- `VALUE`  

  Určuje, že hodnota JSON by měla být načtena namísto celého objektu JSON. To, `<property_list>` na rozdíl od není zalomit promítnutou hodnotu v objektu.  
 
- `DISTINCT`
  
  Určuje, že mají být odebrány duplikáty promítaných vlastností.  

- `<scalar_expression>`  

  Výraz představující hodnotu, která má být vypočítána. Podrobnosti najdete v části [Výrazy Skalární](sql-query-scalar-expressions.md) výrazy.  

## <a name="remarks"></a>Poznámky

Syntaxe `SELECT *` je platná pouze v případě, že klauzule FROM deklarovala přesně jeden alias. `SELECT *`poskytuje projekci identity, která může být užitečná, pokud není potřeba žádná projekce. SELECT * je platný pouze v případě, že klauzule FROM je zadána a zavedena pouze jeden vstupní zdroj.  
  
Oba `SELECT <select_list>` `SELECT *` a jsou "syntaktický cukr" a mohou být alternativně vyjádřeny pomocí jednoduchých příkazů SELECT, jak je znázorněno níže.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   odpovídá:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   odpovídá:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Příklady

Následující příklad dotazu `address` `Families` SELECT `id` `AndersenFamily`vrátí, z jehož shody :

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

### <a name="quoted-property-accessor"></a>Přístupový objekt vlastnosti citovaného
K vlastnostem můžete přistupovat pomocí operátoru vlastnosti citovaného []. Například `SELECT c.grade` a `SELECT c["grade"]` jsou ekvivalentní. Tato syntaxe je užitečná pro únik vlastnosti, která obsahuje mezery, speciální znaky nebo má stejný název jako klíčové slovo SQL nebo vyhrazené slovo.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Vnořené vlastnosti

Následující příklad projekty dvě `f.address.state` vnořené vlastnosti a `f.address.city`.

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

V předchozím příkladu select klauzule potřebuje vytvořit objekt JSON a protože ukázka poskytuje žádný klíč, klauzule používá název `$1`implicitní argument proměnné . Následující dotaz vrátí dvě implicitní `$1` `$2`proměnné argumentu: a .

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

Pokud data obsahují vlastnosti se stejnými názvy jako vyhrazená klíčová slova, jako je například "pořadí" nebo "Skupina", budou dotazy proti těmto dokumentům mít za následek chyby syntaxe. Měli byste explicitně `[]` zahrnout vlastnost v znaku úspěšně spustit dotaz.

Například tady je dokument s názvem `order` vlastnosti `price($)` a vlastností, která obsahuje speciální znaky:

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

Pokud spustíte dotazy, které `order` obsahují `price($)` vlastnost nebo vlastnost, zobrazí se chyba syntaxe.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```
```sql
SELECT * FROM c where c.order.price($) > 50
```
Výsledkem je:

`
Syntax error, incorrect syntax near 'order'
`

Měli byste přepsat stejné dotazy, jak je uvedeno níže:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Ukázky služby Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [KDE doložka](sql-query-where.md)
