---
title: Uživatelsky definované funkce (UDF) v Azure Cosmos DB
description: Přečtěte si o uživatelsky definovaných funkcích v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 98698264f0beb25a8b4f74861f1150ae889d7115
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546331"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Uživatelsky definované funkce (UDF) v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Rozhraní SQL API poskytuje podporu pro uživatelsky definované funkce (UDF). Pomocí skalárního UDF můžete předat nula nebo mnoho argumentů a vrátit výsledek jednoho argumentu. Rozhraní API kontroluje každý argument pro platné hodnoty JSON.  

## <a name="udf-use-cases"></a>Případy použití systému souborů UDF

Rozhraní API rozšiřuje syntaxi SQL pro podporu vlastní logiky aplikace pomocí UDF. UDF můžete zaregistrovat pomocí rozhraní SQL API a odkazovat na ně v dotazech SQL. Na rozdíl od uložených procedur a triggerů jsou UDF jen pro čtení.

Pomocí UDF můžete roztáhnout dotazovací jazyk Azure Cosmos DB. UDF jsou skvělým způsobem, jak vyjádřit složitou obchodní logiku v projekci dotazu.

Nicméně doporučujeme vyhnout se UDF, když:

- Ekvivalentní [Systémová funkce](sql-query-system-functions.md) již v Azure Cosmos DB existuje. Systémové funkce budou vždycky používat méně RU než ekvivalentní formát UDF.
- Formát UDF je jediným filtrem v `WHERE` klauzuli dotazu. Systém UDF nevyužívá index, takže při vyhodnocování systému souborů UDF bude nutné načíst dokumenty. Kombinování dalších predikátů filtru, které používají index, v kombinaci se systémem souborů UDF v `WHERE` klauzuli bude snížen počet dokumentů zpracovávaných systémem souborů UDF.

Pokud v dotazu musíte použít stejnou hodnotu UDF několikrát, měli byste v [poddotazu](sql-query-subquery.md#evaluate-once-and-reference-many-times)na systém souborů UDF odkazovat, abyste mohli použít výraz JOIN k vyhodnocení UDF, ale bude na něj odkazovat mnohokrát.

## <a name="examples"></a>Příklady

Následující příklad registruje UDF v rámci kontejneru položek v databázi Cosmos. Příklad vytvoří příponu UDF, jejíž název je `REGEX_MATCH` . Přijímá dvě hodnoty řetězce JSON a a `input` `pattern` kontroluje, zda první odpovídá vzoru zadanému ve druhém pomocí `string.match()` funkce JavaScriptu.

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

Teď tuto UDF použijte v projekci dotazu. Při volání v rámci dotazů musíte kvalifikovat UDF s předponou pro velká a malá písmena `udf.` .

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

V rámci filtru můžete použít úplný formát UDF s `udf.` předponou, jak je uvedeno v následujícím příkladu:

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

Pokud vlastnosti, na které odkazuje parametr UDF, nejsou v hodnotě JSON dostupné, parametr se považuje za nedefinovaný a volání UDF se přeskočí. Podobně platí, že pokud je výsledek UDF nedefinovaný, není zahrnutý ve výsledku.

Jak ukazují předchozí příklady, UDF integruje sílu jazyka JavaScript s rozhraním SQL API. UDF poskytují bohatě programovatelné rozhraní, které umožňuje komplexní procesní a podmíněné logiky s využitím integrovaných funkcí běhového prostředí jazyka JavaScript. Rozhraní SQL API poskytuje argumenty pro UDF pro každou zdrojovou položku v aktuální fázi zpracování nebo výběru klauzule. Výsledkem je bezproblémové začlenění v celkovém spouštěcím kanálu. V souhrnu jsou UDF skvělé nástroje pro komplexní obchodní logiku jako součást dotazů.

## <a name="next-steps"></a>Další kroky

- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Systémové funkce](sql-query-system-functions.md)
- [Agregace](sql-query-aggregate-functions.md)