---
title: Začínáme s dotazy SQL ve službě Azure Cosmos DB
description: Úvod do dotazů SQL
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 87b275806c06443e37e9e92c35a38b4cde378322
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342508"
---
# <a name="getting-started-with-sql-queries"></a>Začínáme s dotazy SQL

Účty služby Azure SQL API služby Cosmos DB podporuje dotazování položek pomocí jazyka SQL (Structured Query) jako dotazovací jazyk typu JSON. Cíle návrhu dotazovací jazyk služby Azure Cosmos DB je:

* Podporují SQL, jeden z nejčastěji známé a Oblíbené dotazovací jazyky, namísto inventing nový dotazovací jazyk. SQL přes JSON položky poskytuje formální programovací model pro bohaté dotazy.  

* Model programování v jazyce JavaScript můžete použijte jako základ pro dotazovací jazyk. Systém typů, vyhodnocení výrazu a volání funkce v jazyce JavaScript jsou kořeny rozhraní SQL API. Tyto kořeny poskytují přirozené programovací model pro funkce, jako relační projekce, hierarchická navigace mezi položkami formátu JSON, spojení, prostorových dotazů a volání uživatelem definované funkce (UDF) vytvořené zcela v JavaScriptu.

## <a name="upload-sample-data"></a>Nahrání ukázkových dat

Ve vašem účtu SQL rozhraní API Cosmos DB, vytvoření kontejneru s názvem `Families`. Vytvořte dvě jednoduché položky JSON v kontejneru. Většina ukázkové dotazy můžete spustit v dotazu dokumenty Azure Cosmos DB pomocí této datové sady.

### <a name="create-json-items"></a>Vytváření položek JSON

Následující kód vytvoří dvě jednoduché JSON položky o rodiny. Jednoduché položky JSON rodin Andersen a Wakefieldů zahrnují nadřazené položky, podřízené položky a jejich mazlíčků, adresu a informace o registraci. První položka obsahuje řetězce, čísla, logické hodnoty, pole a vnořené vlastnosti.


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

Druhá položka používá `givenName` a `familyName` místo `firstName` a `lastName`.

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

### <a name="query-the-json-items"></a>Dotaz JSON položek

Zkuste několik dotazů na data JSON pochopit některé z klíčových aspektů dotazovací jazyk SQL služby Azure Cosmos DB.

Následující dotaz vrátí položek kde `id` pole odpovídá `AndersenFamily`. Protože se jedná `SELECT *` dotaz, výstup dotazu je úplná položka formátu JSON. Další informace o vyberte syntaxe, naleznete v tématu [příkaz SELECT](sql-query-select.md). 

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

Následující dotaz přeformátuje výstup JSON v odlišném tvaru. Dotaz projekty nové JSON `Family` objekt s dvěma vybraná pole `Name` a `City`, pokud adresa město je stejné jako stav. Tento případ se shoduje s "NY, USA".

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

Následující dotaz vrátí křestní jména všech dětí v rodině jehož `id` odpovídá `WakefieldFamily`, seřazený podle města.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Výsledky jsou:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Poznámky

Předchozí příklady ukazují několik aspektů dotazovací jazyk služby Cosmos DB:  

* Protože rozhraní SQL API pracuje na hodnoty JSON, zabývá entity ve tvaru stromu místo řádků a sloupců. Můžete se podívat do uzlů stromu na libovolnou hloubku libovolného jako `Node1.Node2.Node3…..Nodem`, podobně jako odkaz na dvě části `<table>.<column>` v ANSI SQL.

* Protože dotazovací jazyk pracuje s daty bez schématu, systém typů musí být vázán dynamicky. Stejný výraz může přinést různé typy na různé položky. Výsledek dotazu je platnou hodnotu JSON, ale není zaručeno, že bude pevné schéma.  

* Azure Cosmos DB podporuje pouze striktní položky formátu JSON. Systém typů a výrazy jsou omezeny na řešil pouze typy JSON. Další informace najdete v tématu [JSON specifikace](https://www.json.org/).  

* Kontejneru Cosmos DB je kolekce položek JSON bez schématu. Vztahy v různých položek kontejneru i jsou implicitně zachycena členství ve skupině, není ve primárního klíče a vztahy cizího klíče. Tato funkce je důležitá pro spojení uvnitř položky popsané dále v tomto článku.

## <a name="next-steps"></a>Další postup

- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Klauzule SELECT](sql-query-select.md)
