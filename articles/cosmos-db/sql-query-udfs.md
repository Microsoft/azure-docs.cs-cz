---
title: Uživatelem definované funkce (UDF) v Azure Cosmos DB
description: Další informace o uživatelem definovaných funkcích v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011119"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Uživatelem definované funkce (UDF) v Azure Cosmos DB

Rozhraní SQL API poskytuje podporu pro uživatelem definované funkce (UD). Pomocí skalárních udfs můžete předat v nula nebo mnoho argumentů a vrátit jeden výsledek argumentu. Rozhraní API kontroluje každý argument pro bytí právní hodnoty JSON.  

## <a name="udf-use-cases"></a>Případy použití UDF

Rozhraní API rozšiřuje syntaxi SQL tak, aby podporovala vlastní aplikační logiku pomocí ufls. Udfs můžete zaregistrovat pomocí rozhraní SQL API a odkazovat na ně v dotazech SQL. Na rozdíl od uložené procedury a aktivační události UDFs jsou jen pro čtení.

Pomocí uofs, můžete rozšířit Azure Cosmos DB dotazovací jazyk. UDF s jsou skvělý způsob, jak vyjádřit komplexní obchodní logiku v projekci dotazu.

Doporučujeme však vyhnout se UDFs, když:

- Ekvivalentní [systémová funkce](sql-query-system-functions.md) již v Azure Cosmos DB existuje. Systémové funkce budou vždy používat méně RU než ekvivalentní UDF.
- UDF je jediný filtr `WHERE` v klauzuli dotazu. UDF nevyužívají index, takže vyhodnocení UDF bude vyžadovat načítání dokumentů. Kombinace dalších predikátů filtru, které používají index v `WHERE` kombinaci s udf, v klauzuli sníží počet dokumentů zpracovaných udf.

Pokud je nutné použít stejný UDF vícekrát v dotazu, měli byste odkazovat na UDF v [poddotazu](sql-query-subquery.md#evaluate-once-and-reference-many-times), což vám umožní použít výraz JOIN k vyhodnocení UDF jednou, ale odkazovat mnohokrát.

## <a name="examples"></a>Příklady

Následující příklad registruje UDF pod kontejnerem položek v databázi Cosmos. Příklad vytvoří udf, jehož název je `REGEX_MATCH`. Přijímá dvě hodnoty řetězce JSON `pattern`a a kontroluje, `string.match()` `input` zda první odpovídá vzoru zadanému v druhém pomocí funkce JavaScriptu.

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