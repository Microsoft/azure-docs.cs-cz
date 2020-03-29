---
title: Spuštění dotazu SQL v Azure Cosmos DB
description: Zjistěte, jak vytvořit dotaz SQL a spustit ho v Azure Cosmos DB. Tento článek popisuje, jak vytvořit a spustit dotaz SQL pomocí rozhraní REST API, sady .Net SDK, sady JavaScript SDK a různých dalších sad SDK.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871257"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Spuštění dotazu SQL služby Azure Cosmos DB

Libovolný jazyk, který je schopen provádět požadavky HTTP/HTTPS, může volat rozhraní API SLUŽBY COSMOS DB REST. Cosmos DB také nabízí programovací knihovny pro programovací jazyky .NET, Node.js, JavaScript a Python. Rozhraní REST API a knihovny všechny podporují dotazování prostřednictvím SQL a .NET SDK také podporuje [linq dotazování](sql-query-linq-to-sql.md).

Následující příklady ukazují, jak vytvořit dotaz a odeslat jej proti účtu databáze Cosmos.

## <a name="rest-api"></a><a id="REST-API"></a>ROZHRANÍ API PRO ODPOČINEK

Cosmos DB nabízí otevřený restful programovací model přes HTTP. Model prostředků se skládá ze sady prostředků v rámci databázového účtu, které zřazuje předplatné Azure. Databázový účet se skládá ze sady *databází*, z nichž každá může obsahovat více *kontejnerů*, které zase obsahují *položky*, ufl a další typy prostředků. Každý prostředek Cosmos DB je adresovatelný pomocí logického a stabilního identifikátoru URI. Sada zdrojů se nazývá *informační kanál*. 

Základní model interakce s těmito prostředky `GET`je `PUT` `POST`prostřednictvím `DELETE`sloves PROTOKOLU HTTP , , a , s jejich standardní interpretace. Slouží `POST` k vytvoření nového prostředku, spuštění uložené procedury nebo vydání dotazu Cosmos DB. Dotazy jsou vždy operace jen pro čtení bez vedlejších účinků.

Následující příklady ukazují `POST` dotaz pro sql api proti ukázkové položky. Dotaz má jednoduchý filtr na Vlastnost `name` JSON. `x-ms-documentdb-isquery` Záhlaví a Content-Type: `application/query+json` označují, že operace je dotaz. Nahraďte `mysqlapicosmosdb.documents.azure.com:443` identifikátorEM URI pro váš účet Cosmos DB.

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

Výsledky jsou následující:

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

Další složitější dotaz vrátí více výsledků z spojení:

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

Výsledky jsou následující: 

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

Pokud výsledky dotazu nelze vejít do jedné stránky, rozhraní REST API `x-ms-continuation-token` vrátí token pokračování prostřednictvím hlavičky odpovědi. Klienti mohou stránkovat výsledky zahrnutím záhlaví v následujících výsledcích. Můžete také řídit počet výsledků na `x-ms-max-item-count` stránce prostřednictvím záhlaví čísla.

Pokud dotaz má funkci agregace jako COUNT, stránka dotazu může vrátit částečně agregovně hodnotu pouze na jedné stránce výsledků. Klienti musí provést agregaci druhé úrovně nad těmito výsledky k dosažení konečných výsledků. Například součet přes počty vrácené na jednotlivých stránkách vrátit celkový počet.

Chcete-li spravovat zásady konzistence `x-ms-consistency-level` dat pro dotazy, použijte záhlaví jako ve všech požadavcích rozhraní REST API. Konzistence relace také vyžaduje `x-ms-session-token` ozvěnu poslední hlavičky souboru cookie v požadavku na dotaz. Zásady indexování dotazovaného kontejneru mohou také ovlivnit konzistenci výsledků dotazu. Při výchozím nastavení zásad indexování pro kontejnery je index vždy aktuální s obsahem položky a výsledky dotazu odpovídají konzistenci zvolené pro data. Další informace naleznete v tématu [Úrovně konzistence Azure Cosmos DB][úrovně konzistence].

Pokud nakonfigurovaná zásada indexování v kontejneru nemůže podporovat zadaný dotaz, server Azure Cosmos DB vrátí 400 "Chybný požadavek". Tato chybová zpráva vrátí pro dotazy s cestami explicitně vyloučeny z indexování. Můžete zadat `x-ms-documentdb-query-enable-scan` záhlaví, které umožní dotazu provést skenování, když index není k dispozici.

Podrobné metriky při provádění dotazů `x-ms-documentdb-populatequerymetrics` můžete `true`získat nastavením záhlaví na . Další informace najdete v tématu [metriky dotazů SQL pro Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (Sada SDK..NET)

Sada .NET SDK podporuje dotazování LINQ i SQL. Následující příklad ukazuje, jak provést předchozí dotaz filtru s rozhraním .NET:

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

Následující příklad porovnává dvě vlastnosti rovnosti v rámci každé položky a používá anonymní projekce.

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

Následující příklad ukazuje spojení, vyjádřené `SelectMany`prostřednictvím LINQ .

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

Klient .NET automaticky iterates přes všechny stránky `foreach` výsledků dotazu v blocích, jak je znázorněno v předchozím příkladu. Možnosti dotazu zavedené v části [rozhraní REST API](#REST-API) jsou také k `FeedOptions` `FeedResponse` dispozici v `CreateDocumentQuery` .NET SDK pomocí a třídy v metodě. Počet stránek můžete řídit pomocí `MaxItemCount` nastavení.

Stránkování můžete také explicitně `IDocumentQueryable` řídit `IQueryable` vytvořením pomocí `ResponseContinuationToken` objektu, potom `RequestContinuationToken` čtením hodnot a jejich předáním zpět jako v `FeedOptions`. Můžete nastavit `EnableScanInQuery` povolení prohledávání, pokud dotaz není podporován nakonfigurovanou zásadou indexování. Pro dělené kontejnery `PartitionKey` můžete použít ke spuštění dotazu proti jeden oddíl, i když Azure Cosmos DB můžete automaticky extrahovat z textu dotazu. Můžete použít `EnableCrossPartitionQuery` ke spuštění dotazů proti více oddílů.

Další ukázky rozhraní .NET s dotazy najdete v tématu [ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3) na GitHubu.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>Rozhraní API na straně serveru JavaScript

Azure Cosmos DB poskytuje programovací model pro provádění aplikační logiky založené na [Jazyce JavaScript](stored-procedures-triggers-udfs.md) přímo na kontejnerech pomocí uložených procedur a aktivačních událostí. Logika JavaScriptu registrovaná na úrovni kontejneru pak může vydávat databázové operace na položky daného kontejneru, zabalené v transakcích okolí ACID.

Následující příklad ukazuje, `queryDocuments` jak používat v rozhraní API serveru JavaScript provádět dotazy z uvnitř uložené procedury a aktivační události:

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

## <a name="next-steps"></a>Další kroky

- [Úvod do Azure Cosmos DB](introduction.md)
- [Ukázky služby Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md)
