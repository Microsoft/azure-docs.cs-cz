---
title: Začínáme s dotazy SQL v Azure Cosmos DB
description: Naučte se používat dotazy SQL k dotazování dat z Azure Cosmos DB. Ukázková data můžete nahrát do kontejneru v Azure Cosmos DB a dotazovat se na ně.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: tisande
ms.openlocfilehash: f4ee0c0af6939e71f696fc900ec2ab1343ca91df
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802510"
---
# <a name="getting-started-with-sql-queries"></a>Začínáme s příkazy jazyka SQL

V Azure Cosmos DB účty rozhraní SQL API existují dva způsoby, jak číst data:

**Čtení bodů** – vyhledávání klíč/hodnota můžete provést na základě *ID jedné položky* a klíče oddílu. Kombinace *ID položky* a klíče oddílu je klíč a samotná položka je hodnota. V případě dokumentu s 1 KBm čte bod obvykle náklady 1 [jednotka požadavku](request-units.md) s latencí pod 10 ms. Čtení bodů vrátí jednu položku.

**Dotazy SQL** – můžete zadávat dotazy na data zápisem dotazů pomocí jazyk SQL (Structured Query Language) (SQL) jako dotazovacího jazyka JSON. Dotazy mají vždycky náklady minimálně 2,3 jednotek žádostí a obecně budou mít větší a větší latenci, než je čtení bodů. Dotazy mohou vracet mnoho položek.

Většina úloh pro čtení v Azure Cosmos DB využívá kombinaci obou čtení bodů i dotazů SQL. Pokud potřebujete jen číst jednu položku, jsou čtení bodů levnější a rychlejší než dotazy. Čtení bodů není nutné používat dotazovací modul pro přístup k datům a může přímo číst data. Samozřejmě to není možné, aby všechny úlohy výhradně četly data pomocí čtení bodů, takže podpora SQL jako dotazovacího jazyka a [nezávislá indexování](index-overview.md) nabízí pružnější způsob, jak získat přístup k datům.

Tady je několik příkladů, jak číst body pomocí jednotlivých SDK:

- [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet&preserve-view=true)
- [Java SDK](https://docs.microsoft.com/java/api/com.azure.cosmos.cosmoscontainer.readitem?view=azure-java-stable&preserve-view=true#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Node.js SDK](https://docs.microsoft.com/javascript/api/@azure/cosmos/item?view=azure-node-latest&preserve-view=true#read-requestoptions-)
- [Python SDK](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.containerproxy?view=azure-python&preserve-view=true#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

Zbývající část tohoto dokumentu ukazuje, jak začít psát dotazy SQL v Azure Cosmos DB. Dotazy SQL je možné spouštět buď pomocí sady SDK, nebo Azure Portal.

## <a name="upload-sample-data"></a>Nahrání ukázkových dat

Ve vašem účtu Cosmos DB API SQL vytvořte kontejner s názvem `Families` . Vytvořte v kontejneru dvě jednoduché položky JSON. Většinu ukázkových dotazů můžete spustit v dokumentaci k Azure Cosmos DB dotazování pomocí této datové sady.

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

* Azure Cosmos DB podporuje pouze striktní položky JSON. Systém typů a výrazy jsou omezené tak, aby pracovaly pouze s typy JSON. Další informace najdete v tématu [specifikace JSON](https://www.json.org/).  

* Kontejner Cosmos je kolekce položek JSON bez schématu. Vztahy v rámci a napříč položkami kontejneru jsou implicitně zachyceny omezením, nikoli podle primárního klíče a vztahů cizího klíče. Tato funkce je důležitá pro spojení uvnitř položky, která jsou popsána dále v tomto článku.

## <a name="next-steps"></a>Další kroky

- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzule SELECT](sql-query-select.md)
