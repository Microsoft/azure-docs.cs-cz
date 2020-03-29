---
title: Začínáme s dotazy SQL v Azure Cosmos DB
description: Zjistěte, jak pomocí dotazů SQL dotazovat data z Azure Cosmos DB. Ukázková data můžete nahrát do kontejneru v Azure Cosmos DB a zadat dotaz.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873331"
---
# <a name="getting-started-with-sql-queries"></a>Začínáme s dotazy SQL

Účty SQL API Azure Cosmos DB podporují dotazování položek pomocí strukturovaného dotazovacího jazyka (SQL) jako dotazovacího jazyka JSON. Cíle návrhu dotazovacího jazyka Azure Cosmos DB jsou:

* Podpora SQL, jeden z nejznámějších a nejoblíbenějších dotazovacích jazyků, namísto vymýšlení nového dotazovacího jazyka. SQL poskytuje formální programovací model pro bohaté dotazy přes položky JSON.  

* Jako základ pro dotazovací jazyk použijte programovací model JavaScriptu. Systém typů JavaScriptu, vyhodnocení výrazu a vyvolání funkce jsou kořeny rozhraní SQL API. Tyto kořeny poskytují přirozený programovací model pro funkce, jako jsou relační projekce, hierarchická navigace mezi položkami JSON, vlastní spojení, prostorové dotazy a vyvolání uživatelem definovaných funkcí (UOF) napsaných výhradně v JavaScriptu.

## <a name="upload-sample-data"></a>Nahrání ukázkových dat

V účtu SQL API Cosmos DB `Families`vytvořte kontejner s názvem . Vytvořte dvě jednoduché položky JSON v kontejneru. Většinu ukázkových dotazů můžete spustit v dokumentech dotazu Azure Cosmos DB pomocí této sady dat.

### <a name="create-json-items"></a>Vytvořit položky JSON

Následující kód vytvoří dvě jednoduché položky JSON o rodinách. Jednoduché položky JSON pro rodiny Andersen a Wakefield zahrnují rodiče, děti a jejich domácí mazlíčky, adresu a registrační informace. První položka má řetězce, čísla, logické hodnoty, pole a vnořené vlastnosti.


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

Druhá položka `givenName` používá `familyName` a `firstName` `lastName`místo a .

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

### <a name="query-the-json-items"></a>Dotaz na položky JSON

Vyzkoušejte několik dotazů na data JSON, abyste porozuměli některým klíčovým aspektům dotazovacího jazyka SQL služby Azure Cosmos DB.

Následující dotaz vrátí položky, ve kterých se `id` pole shoduje `AndersenFamily`. Vzhledem k `SELECT *` tomu, že se jedná o dotaz, výstup dotazu je úplná položka JSON. Další informace o syntaxi SELECT naleznete v [příkazu SELECT](sql-query-select.md). 

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

Následující dotaz přeformátuje výstup JSON do jiného tvaru. Dotaz promítne nový `Family` objekt JSON `Name` se `City`dvěma vybranými poli a , pokud je město adresy stejné jako stát. "NY, NY" odpovídá tomuto případu.

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

Následující dotaz vrátí všechna uvedená jména dětí `id` `WakefieldFamily`v rodině, jejíž shody , seřazené podle města.

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

Předchozí příklady ukazují několik aspektů dotazovacího jazyka Cosmos DB:  

* Vzhledem k tomu, že rozhraní SQL API funguje na hodnotách JSON, zabývá se entitami ve tvaru stromu namísto řádků a sloupců. Můžete odkazovat na uzly stromu v `Node1.Node2.Node3…..Nodem`libovolné hloubce, jako je `<table>.<column>` , podobně jako dvoudílný odkaz v ANSI SQL.

* Vzhledem k tomu, že dotazovací jazyk pracuje s daty bez schématu, musí být systém typů dynamicky vázán. Stejný výraz může přinést různé typy na různé položky. Výsledkem dotazu je platná hodnota JSON, ale není zaručeno, že bude pevné schéma.  

* Azure Cosmos DB podporuje pouze striktní položky JSON. Systém typů a výrazy jsou omezeny pouze na typy JSON. Další informace naleznete ve [specifikaci JSON](https://www.json.org/).  

* Kontejner Cosmos je kolekce položek JSON bez schématu. Vztahy v rámci a mezi položkami kontejneru jsou implicitně zachyceny uzavřením, nikoli primárním klíčem a vztahy cizího klíče. Tato funkce je důležitá pro spojení uvnitř položky popsané dále v tomto článku.

## <a name="next-steps"></a>Další kroky

- [Úvod do Azure Cosmos DB](introduction.md)
- [Ukázky služby Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzule SELECT](sql-query-select.md)
