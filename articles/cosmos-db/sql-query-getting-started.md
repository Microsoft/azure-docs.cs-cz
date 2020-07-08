---
title: Začínáme s dotazy SQL v Azure Cosmos DB
description: Naučte se používat dotazy SQL k dotazování dat z Azure Cosmos DB. Ukázková data můžete nahrát do kontejneru v Azure Cosmos DB a dotazovat se na ně.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74873331"
---
# <a name="getting-started-with-sql-queries"></a>Začínáme s dotazy SQL

Azure Cosmos DB účty rozhraní SQL API podporují dotazování položek pomocí jazyk SQL (Structured Query Language) (SQL) jako dotazovacího jazyka JSON. Cílem Azure Cosmos DBho dotazovacího jazyka je:

* Podporovat SQL jeden z nejoblíbenějších a oblíbených dotazovacích jazyků, místo aby se dopracoval nový dotazovací jazyk. SQL poskytuje formální programovací model pro bohatou dotazy nad položkami JSON.  

* Použijte programovací model jazyka JavaScript jako základ dotazovacího jazyka. Rozhraní API SQL jsou kořeny typu systém, vyhodnocení výrazu a volání funkce. Tyto kořeny poskytují přirozený programovací model pro funkce, jako jsou relační projekce, hierarchická navigace mezi položkami JSON, vlastní spojení, prostorové dotazy a volání uživatelsky definovaných funkcí (UDF) zapsané zcela v JavaScriptu.

## <a name="upload-sample-data"></a>Nahrání ukázkových dat

Ve vašem účtu Cosmos DB API SQL vytvořte kontejner s názvem `Families` . Vytvořte v kontejneru dvě jednoduché položky JSON. Většinu ukázkových dotazů můžete spustit v Azure Cosmos DB dotazů na dokumenty pomocí této datové sady.

### <a name="create-json-items"></a>Vytvoření položek JSON

Následující kód vytvoří dvě jednoduché položky JSON o rodinách. K jednoduchým položkám JSON pro rodiny Andersen a Wakefieldů patří rodiče, děti a jejich domácí, adresa a registrační informace. První položka obsahuje řetězce, čísla, logické hodnoty, pole a vnořené vlastnosti.


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Druhá položka používá `givenName` a `familyName` místo `firstName` a `lastName` .

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Dotazování na položky JSON

Vyzkoušejte několik dotazů na data JSON, abyste se seznámili s některými klíčovými aspekty dotazovacího jazyka SQL v Azure Cosmos DB.

Následující dotaz vrátí položky, kde se `id` pole shoduje `AndersenFamily` . Vzhledem k tomu, že se jedná o `SELECT *` dotaz, je výstupem dotazu úplná položka JSON. Další informace o syntaxi výběru naleznete v tématu [Select Statement](sql-query-select.md). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky dotazu jsou: 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

Následující dotaz přeformátuje výstup JSON na jiný tvar. Dotaz vychází z nového `Family` objektu JSON se dvěma vybranými poli `Name` a v `City` případě, že adresa města je stejná jako stav. "NY" NY "odpovídá tomuto případu.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Výsledky dotazu jsou:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Následující dotaz vrátí všechny křestní názvy podřízených objektů v rodině `id` , jejichž shoda `WakefieldFamily` je seřazena podle města.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Výsledky jsou následující:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Poznámky

Předchozí příklady ukazují několik aspektů Cosmos DB dotazovacího jazyka:  

* Vzhledem k tomu, že rozhraní SQL API funguje na hodnotách JSON, pracuje s entitami ve tvaru stromu namísto řádků a sloupců. Můžete odkazovat na uzly stromu libovolné hloubky, například `Node1.Node2.Node3…..Nodem` , podobně jako na odkaz na dvě části `<table>.<column>` v ANSI SQL.

* Vzhledem k tomu, že dotazovací jazyk pracuje s daty bez schématu, musí být systém typů svázán dynamicky. Stejný výraz může vracet různé typy v různých položkách. Výsledek dotazu je platná hodnota JSON, ale nezaručujeme, že by mělo být pevně dané schéma.  

* Azure Cosmos DB podporuje pouze striktní položky JSON. Systém typů a výrazy jsou omezené, aby se jednalo jenom o typy JSON. Další informace najdete v tématu [specifikace JSON](https://www.json.org/).  

* Kontejner Cosmos je kolekce položek JSON bez schématu. Vztahy v rámci a napříč položkami kontejneru jsou implicitně zachyceny omezením, nikoli podle primárního klíče a vztahů cizího klíče. Tato funkce je důležitá pro spojení uvnitř položky, která jsou popsána dále v tomto článku.

## <a name="next-steps"></a>Další kroky

- [Úvod do Azure Cosmos DB](introduction.md)
- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzule SELECT](sql-query-select.md)
