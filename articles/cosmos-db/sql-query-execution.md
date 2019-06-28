---
title: Spuštění dotazu SQL ve službě Azure Cosmos DB
description: Další informace o spuštění dotazu SQL ve službě Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: e4e26b658bd29e4589be40e4d29935059836c909
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342582"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB SQL provádění dotazů

Jakémkoli jazyce schopném zasílat požadavky HTTP/HTTPS můžete volat rozhraní API REST služby Cosmos DB. Cosmos DB dále nabízí programovací knihovny pro .NET, Node.js, JavaScript a Python programovacích jazyků. Dotazování prostřednictvím SQL podporují rozhraní API REST a knihovny, a sady .NET SDK podporuje také [dotazování LINQ na](sql-query-linq-to-sql.md).

Následující příklady ukazují, jak vytvořit dotaz a odeslat ji proti účtu databáze Cosmos DB.

## <a id="REST-API"></a>ROZHRANÍ REST API

Cosmos DB nabízí otevřete programovací model RESTful přes HTTP. Modelu prostředků, který se skládá ze sady prostředků v rámci databázový účet, který ustanovení předplatnému Azure. Databázový účet se skládá ze sady *databází*, z nichž každá může obsahovat více *kontejnery*, které pak obsahovat *položky*, funkcí UDF a další typy prostředků. Každý prostředek Cosmos DB je adresovatelné logické a stabilní identifikátor URI. Je volána sadu prostředků *kanálu*. 

Pomocí příkazů HTTP je model základní interakce s těmito prostředky `GET`, `PUT`, `POST`, a `DELETE`, s jejich standardní interpretace. Použití `POST` Pokud chcete vytvořit nový prostředek, spustit uloženou proceduru nebo vydat dotaz Cosmos DB. Dotazy jsou vždy jen pro čtení operací s žádným vedlejším účinkům.

Následující příklady ukazují `POST` pro rozhraní SQL API dotazu na ukázkové položky. Dotaz musí jednoduchý filtr na JSON `name` vlastnost. `x-ms-documentdb-isquery` A Content-Type: `application/query+json` záhlaví označují, že operace je dotaz. Nahraďte `mysqlapicosmosdb.documents.azure.com:443` s identifikátorem URI pro váš účet služby Cosmos DB.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Výsledky jsou:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

Dál se složitější dotaz vrátí více výsledků ze spojení:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

Výsledky jsou: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Pokud výsledky dotazu se nemůže vejít na jedné stránce, rozhraní REST API vrátí token pro pokračování prostřednictvím `x-ms-continuation-token` hlavičky odpovědi. Klienti můžou stránkovat výsledky včetně záhlaví v dalších výsledků. Můžete také řídit počet výsledků na stránku prostřednictvím `x-ms-max-item-count` číslo hlavičky.

Pokud dotaz obsahuje agregační funkci, jako je počet, na stránce dotaz může vracet částečně agregovanou hodnotu pouze jednotlivé stránky výsledků. Klienti musí provést agregace druhé úrovně přes tyto výsledky poslední výsledky. Například součet přes počty vrácené v jednotlivých stránek vrátit celkový počet.

Chcete-li spravovat zásady konzistence dat pro dotazy, použijte `x-ms-consistency-level` záhlaví jako všechny požadavky rozhraní REST API. Konzistence typu relace vyžaduje také nejnovější přečtou `x-ms-session-token` hlavička cookie v dotazu žádosti. Zásady indexování dotazované kontejner může také ovlivnit konzistence výsledky dotazu. S výchozí nastavení zásad pro kontejnery indexování, index je vždy s obsahem položky aktuální a výsledky dotazu odpovídat konzistence, které jste zvolili pro data. Další informace najdete v článku [Azure Cosmos DB úrovně konzistence] [úrovně konzistence].

Pokud nakonfigurované zásady indexování v kontejneru nepodporuje zadaný dotaz, server služby Azure Cosmos DB vrátí 400 "Chybný požadavek". Tato chybová zpráva se vrací pro dotazy s cestami, které jsou explicitně vyloučené z indexování. Můžete zadat `x-ms-documentdb-query-enable-scan` záhlaví umožňující dotazu má provést kontrola indexu není k dispozici.

Můžete získat podrobné metriky spouštění dotazů nastavením `x-ms-documentdb-populatequerymetrics` záhlaví `true`. Další informace najdete v tématu [metriky dotaz SQL pro službu Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C#(SADU .NET SDK)

Sady .NET SDK podporuje LINQ a SQL dotazování. Následující příklad ukazuje, jak provést předchozí dotaz filtru s .NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Následující příklad porovná dvě vlastnosti rovnost v rámci každé položky a používá anonymní projekce.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Další příklad ukazuje, spojení, vyjádřené pomocí LINQ `SelectMany`.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

.NET client automaticky Iteruje přes všechny stránky výsledků dotazu v `foreach` blokuje, jak je znázorněno v předchozím příkladu. Počínaje možnosti dotazu [rozhraní REST API](#REST-API) části jsou také k dispozici v sadě .NET SDK pomocí `FeedOptions` a `FeedResponse` tříd v `CreateDocumentQuery` metoda. Počet stránek, můžete řídit pomocí `MaxItemCount` nastavení.

Můžete také explicitně kontrolovat stránkování tak, že vytvoříte `IDocumentQueryable` pomocí `IQueryable` objekt, potom načtením `ResponseContinuationToken` hodnoty a jejich předávání zpátky jako `RequestContinuationToken` v `FeedOptions`. Můžete nastavit `EnableScanInQuery` povolení vyhledávání, když dotaz nepodporuje nakonfigurované zásady indexování. Pro dělené kontejnerů, můžete použít `PartitionKey` ke spuštění dotazu jeden oddíl, i když službu Azure Cosmos DB můžete automaticky extrahovat to z text dotazu. Můžete použít `EnableCrossPartitionQuery` ke spouštění dotazů na několik oddílů.

Další ukázky .NET pomocí dotazů, najdete v článku [ukázek Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet) v Githubu.

## <a id="JavaScript-server-side-API"></a>Rozhraní API pro JavaScript na straně serveru

Azure Cosmos DB poskytuje programovací model pro [provádění jazyka JavaScript aplikace založené na](stored-procedures-triggers-udfs.md) logiku přímo na kontejnery pomocí uložených procedur a aktivačních událostí. Logiky JavaScript zaregistrovaných na úrovni kontejneru pak můžou provádět databázové operace na položky daného kontejneru zabalené v okolí transakce ACID.

Následující příklad ukazuje, jak používat `queryDocuments` serveru JavaScript API posílat dotazy z vnitřního uložených procedur a aktivačních událostí:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>Další postup

- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Azure Cosmos DB úrovně konzistence](consistency-levels.md)
