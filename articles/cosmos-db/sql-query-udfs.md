---
title: Uživatelem definované funkce (UDF) v Azure Cosmos DB
description: Další informace o uživatelem definovaných funkcích v Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69614329"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Uživatelem definované funkce (UDF) v Azure Cosmos DB

Rozhraní SQL API poskytuje podporu pro uživatelem definované funkce (UD). Pomocí skalárních udfs můžete předat v nula nebo mnoho argumentů a vrátit jeden výsledek argumentu. Rozhraní API kontroluje každý argument pro bytí právní hodnoty JSON.  

Rozhraní API rozšiřuje syntaxi SQL tak, aby podporovala vlastní aplikační logiku pomocí ufls. Udfs můžete zaregistrovat pomocí rozhraní SQL API a odkazovat na ně v dotazech SQL. Ve skutečnosti jsou UDjsou nádherně navrženy tak, aby volaly z dotazů. Jako důsledek, UDnemají přístup k objektu kontextu, jako jsou jiné typy JavaScriptu, jako jsou uložené procedury a aktivační události. Dotazy jsou jen pro čtení a lze je spustit na primární nebo sekundární repliky. UD, na rozdíl od jiných typů JavaScriptu, jsou navrženy tak, aby spouštět na sekundární repliky.

Následující příklad registruje UDF pod kontejnerem položek v databázi Cosmos. Příklad vytvoří udf, jehož název je `REGEX_MATCH`. Přijímá dvě hodnoty řetězce JSON `pattern`a a kontroluje, `string.match()` `input` zda první odpovídá vzoru zadanému v druhém pomocí funkce JavaScriptu.

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

Nyní použijte tento UDF v projekci dotazu. Je nutné kvalifikovat UDFs s `udf.` předponou rozlišování velkých a malých písmen při jejich volání z v rámci dotazů.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Výsledky jsou následující:

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

Můžete použít udf kvalifikované `udf.` s předponou uvnitř filtru, jako v následujícím příkladu:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Výsledky jsou následující:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

V podstatě UD jsou platné skalární výrazy, které můžete použít v projekcích i filtrech.

Chcete-li rozšířit mocninu uofs, podívejte se na jiný příklad s podmíněnou logikou:

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

Následující příklad používá UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Výsledky jsou následující:

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

Pokud vlastnosti uvedené parametry UDF nejsou k dispozici v hodnotě JSON, parametr je považován za nedefinovaný a vyvolání UDF je přeskočeno. Podobně pokud výsledek UDF není definován, není zahrnut a ve výsledku.

Jak ukazují předchozí příklady, UDfs integrovat sílu jazyka JavaScript s SQL API. UOF poskytují bohaté programovatelné rozhraní pro komplexní procedurální podmíněnou logiku pomocí vestavěných funkcí javascriptového běhu. Rozhraní SQL API poskytuje argumenty UDpro každou zdrojovou položku v aktuální fázi zpracování klauzule WHERE nebo SELECT. Výsledek je bezproblémově začleněndo celkového kanálu provádění. Stručně řečeno, UD jsou skvělé nástroje pro komplexní obchodní logiku jako součást dotazů.

## <a name="next-steps"></a>Další kroky

- [Úvod do Azure Cosmos DB](introduction.md)
- [Systémové funkce](sql-query-system-functions.md)
- [Agregace](sql-query-aggregates.md)