---
title: Uživatelem definované funkce (UDF) ve službě Azure Cosmos DB
description: Další informace o uživatelem definovaných funkcí ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: e168e450230720f4ad78516e6edcdc3aa08ba3e1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342835"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Uživatelem definované funkce (UDF) ve službě Azure Cosmos DB

Rozhraní SQL API poskytuje podporu pro uživatelem definované funkce (UDF). Skalární funkce UDF můžete předat argumenty nula nebo více a vrácení výsledku jeden argument. Rozhraní API zkontroluje každý argument se platné hodnoty JSON.  

Rozhraní API rozšiřuje syntaxe jazyka SQL pro podporu vlastní logiky aplikace pomocí funkcí UDF. Může vytvářet UDF s rozhraním SQL API a referenční v dotazech SQL. Ve skutečnosti UDF jsou exquisitely navržené k volání z dotazů. Jako důsledkem funkcí UDF nebudou mít přístup k objektu kontextu stejně jako jiné typy jazyka JavaScript, jako jsou uložené procedury a triggery. Dotazy jsou jen pro čtení a lze spustit buď na primární nebo sekundární repliky. Na rozdíl od jiných typů JavaScript, funkcí UDF jsou navrženy ke spouštění na sekundárních replikách.

Následující příklad registruje UDF pod kontejner položek v databázi Cosmos DB. V příkladu se vytvoří UDF, jehož název je `REGEX_MATCH`. Přijímá dva řetězcové hodnoty JSON `input` a `pattern`, a zkontroluje, jestli první odpovídá vzoru zadané v druhý pomocí jazyka JavaScript `string.match()` funkce.

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

Teď pomocí tohoto systému souborů UDF v projekci dotazu. Malá a velká písmena předponou musí kvalifikovat UDF `udf.` při volání z v rámci dotazů.

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

Můžete použít UDF kvalifikován s `udf.` předpona rámci filtru, stejně jako v následujícím příkladu:

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

V podstatě UDF jsou platná skalární výrazy, které můžete použít v projekce a filtry.

Rozšíření power uživatelem definovanými funkcemi, podívejte se na další příklad s podmíněnou logiku:

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

V následujícím příkladu vykonává UDF:

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

Pokud uvedené vlastnosti podle UDF nejsou k dispozici v hodnotě JSON, parametry, se považuje za parametr undefined a vyvolání UDF se přeskočí. Podobně pokud výsledek UDF není definován, není součástí výsledku.

Jako v předchozích příkladech, integrace UDF sílu jazyka JavaScript pomocí rozhraní SQL API. UDF poskytuje také bohaté rozhraní příkazů programovatelný provádět komplexní procesní, podmíněné logiky pomocí integrované funkce modulu runtime jazyka JavaScript. Rozhraní SQL API poskytuje argumenty UDF pro každou položku zdroje v aktuální WHERE nebo klauzuli SELECT fáze zpracování. Výsledek je bezproblémově součástí celkového spuštění kanálu. Stručně řečeno funkce UDF představují vynikající nástroj provádět komplexní obchodní logiku jako součást dotazy.

## <a name="next-steps"></a>Další postup

- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Funkce systému](sql-query-system-functions.md)
- [agregace](sql-query-aggregates.md)