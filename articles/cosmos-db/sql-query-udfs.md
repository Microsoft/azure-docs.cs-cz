---
title: Uživatelsky definované funkce (UDF) v Azure Cosmos DB
description: Přečtěte si o uživatelsky definovaných funkcích v Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614329"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Uživatelsky definované funkce (UDF) v Azure Cosmos DB

Rozhraní SQL API poskytuje podporu pro uživatelsky definované funkce (UDF). Pomocí skalárního UDF můžete předat nula nebo mnoho argumentů a vrátit výsledek jednoho argumentu. Rozhraní API kontroluje každý argument pro platné hodnoty JSON.  

Rozhraní API rozšiřuje syntaxi SQL pro podporu vlastní logiky aplikace pomocí UDF. UDF můžete zaregistrovat pomocí rozhraní SQL API a odkazovat na ně v dotazech SQL. Ve skutečnosti UDF jsou exquisitely navržené k volání z dotazů. UDF jako Corollary nemají přístup k objektu kontextu, jako jsou jiné typy JavaScriptu, jako jsou například uložené procedury a triggery. Dotazy jsou jen pro čtení a můžou běžet buď na primárních, nebo sekundárních replikách. UDF na rozdíl od jiných typů JavaScriptu je navržena pro spouštění na sekundárních replikách.

Následující příklad registruje UDF v rámci kontejneru položek v databázi Cosmos. Příklad vytvoří příponu UDF, jejíž název je `REGEX_MATCH`. Přijímá dvě hodnoty `input` řetězce JSON a `pattern`a kontroluje, zda první odpovídá vzoru zadanému ve druhém pomocí `string.match()` funkce JavaScriptu.

## <a name="examples"></a>Příklady

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Teď tuto UDF použijte v projekci dotazu. Při volání v rámci dotazů musíte kvalifikovat UDF s předponou `udf.` pro velká a malá písmena.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Výsledky jsou:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

V rámci filtru můžete použít úplný formát UDF `udf.` s předponou, jak je uvedeno v následujícím příkladu:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Výsledky jsou:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

V podstatě jsou UDF platné skalární výrazy, které lze použít v projekcích a filtrech.

Pokud chcete rozšířit výkon UDF, podívejte se na jiný příklad s využitím podmíněné logiky:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

V následujícím příkladu se systém souborů UDF uplatňuje:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Výsledky jsou:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Pokud vlastnosti, na které odkazuje parametr UDF, nejsou v hodnotě JSON dostupné, parametr se považuje za nedefinovaný a volání UDF se přeskočí. Podobně platí, že pokud je výsledek UDF nedefinovaný, není zahrnutý ve výsledku.

Jak ukazují předchozí příklady, UDF integruje sílu jazyka JavaScript s rozhraním SQL API. UDF poskytují bohatě programovatelné rozhraní, které umožňuje komplexní procesní a podmíněné logiky s využitím integrovaných funkcí běhového prostředí jazyka JavaScript. Rozhraní SQL API poskytuje argumenty pro UDF pro každou zdrojovou položku v aktuální fázi zpracování nebo výběru klauzule. Výsledkem je bezproblémové začlenění v celkovém spouštěcím kanálu. V souhrnu jsou UDF skvělé nástroje pro komplexní obchodní logiku jako součást dotazů.

## <a name="next-steps"></a>Další postup

- [Úvod do Azure Cosmos DB](introduction.md)
- [Systémové funkce](sql-query-system-functions.md)
- [Agregace](sql-query-aggregates.md)