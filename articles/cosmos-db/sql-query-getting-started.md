---
title: Začínáme s dotazy SQL v Azure Cosmos DB
description: Naučte se používat dotazy SQL k dotazování dat z Azure Cosmos DB. Ukázková data můžete nahrát do kontejneru v Azure Cosmos DB a dotazovat se na ně.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: 21b26bae5fe04d48214a0c62cbb530ea89ad1448
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426004"
---
# <a name="getting-started-with-sql-queries"></a>Začínáme s příkazy jazyka SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

V Azure Cosmos DB účty rozhraní SQL API existují dva způsoby, jak číst data:

**Čtení bodů** – vyhledávání klíč/hodnota můžete provést na základě *ID jedné položky* a klíče oddílu. Kombinace *ID položky* a klíče oddílu je klíč a samotná položka je hodnota. V případě dokumentu s 1 KBm čte bod obvykle náklady 1 [jednotka požadavku](request-units.md) s latencí pod 10 ms. Čtení bodů vrátí jednu položku.

Tady je několik příkladů, jak **číst body** pomocí jednotlivých SDK:

- [.NET SDK](/dotnet/api/microsoft.azure.cosmos.container.readitemasync)
- [Java SDK](/java/api/com.azure.cosmos.cosmoscontainer.readitem#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Node.js SDK](/javascript/api/@azure/cosmos/item#read-requestoptions-)
- [Python SDK](/python/api/azure-cosmos/azure.cosmos.containerproxy?preserve-view=true&view=azure-python#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

**Dotazy SQL** – můžete zadávat dotazy na data zápisem dotazů pomocí jazyk SQL (Structured Query Language) (SQL) jako dotazovacího jazyka JSON. Dotazy mají vždycky náklady minimálně 2,3 jednotek žádostí a obecně budou mít větší a větší latenci, než je čtení bodů. Dotazy mohou vracet mnoho položek.

Většina úloh pro čtení v Azure Cosmos DB využívá kombinaci obou čtení bodů i dotazů SQL. Pokud potřebujete jen číst jednu položku, jsou čtení bodů levnější a rychlejší než dotazy. Čtení bodů není nutné používat dotazovací modul pro přístup k datům a může přímo číst data. Samozřejmě to není možné, aby všechny úlohy výhradně četly data pomocí čtení bodů, takže podpora SQL jako dotazovacího jazyka a [nezávislá indexování](index-overview.md) nabízí pružnější způsob, jak získat přístup k datům.

Tady je několik příkladů, jak provádět **dotazy SQL** pomocí každé sady SDK:

- [.NET SDK](./sql-api-dotnet-v3sdk-samples.md#query-examples)
- [Java SDK](./sql-api-java-sdk-samples.md#query-examples)
- [Node.js SDK](./sql-api-nodejs-samples.md#item-examples)
- [Python SDK](./sql-api-python-samples.md#item-examples)

Zbývající část tohoto dokumentu ukazuje, jak začít psát dotazy SQL v Azure Cosmos DB. Dotazy SQL je možné spouštět buď pomocí sady SDK, nebo Azure Portal.

## <a name="upload-sample-data"></a>Nahrání ukázkových dat

Ve vašem účtu Cosmos DB API SQL otevřete [Průzkumník dat](./data-explorer.md) a vytvořte kontejner s názvem `Families` . Po vytvoření je můžete pomocí prohlížeče datových struktur vyhledat a otevřít. Ve vašem `Families` kontejneru se zobrazí `Items` možnost vpravo pod názvem kontejneru. Tuto možnost otevřete a v řádku nabídek uprostřed obrazovky se zobrazí tlačítko pro vytvoření nové položky. Tato funkce slouží k vytvoření položek JSON níže.

### <a name="create-json-items"></a>Vytvoření položek JSON

Následující dvě položky JSON jsou dokumenty o rodinách Andersen a Wakefieldů. Zahrnují rodiče, podřízené položky a jejich domácí, adresu a registrační informace. 

První položka obsahuje řetězce, čísla, logické hodnoty, pole a vnořené vlastnosti:

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

Druhá položka používá `givenName` a `familyName` místo `firstName` a `lastName` :

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

* Kontejner Cosmos je kolekce položek JSON bez schématu. Vztahy v rámci a napříč položkami kontejneru jsou implicitně zachyceny omezením, nikoli podle primárního klíče a vztahů cizího klíče. Tato funkce je důležitá pro spojení uvnitř položky, která jsou popsána v tématu [joins in Azure Cosmos DB](sql-query-join.md).

## <a name="next-steps"></a>Další kroky

- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klauzule SELECT](sql-query-select.md)
