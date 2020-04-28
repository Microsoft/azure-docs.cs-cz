---
title: Provádění dotazu SQL v Azure Cosmos DB
description: Naučte se vytvořit dotaz SQL a spustit ho v Azure Cosmos DB. Tento článek popisuje, jak vytvořit a spustit dotaz SQL pomocí REST API, sady .NET SDK, sady JavaScript SDK a různých dalších sad SDK.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74871257"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB provádění dotazů SQL

Libovolný jazyk schopný provádět požadavky HTTP/HTTPS může volat Cosmos DB REST API. Cosmos DB také nabízí programovací knihovny pro programovací jazyky .NET, Node. js, JavaScript a Python. REST API a knihovny podporují dotazování prostřednictvím SQL a sada .NET SDK také podporuje [dotazování LINQ](sql-query-linq-to-sql.md).

Následující příklady ukazují, jak vytvořit dotaz a odeslat ho proti Cosmos databázovému účtu.

## <a name="rest-api"></a><a id="REST-API"></a>REST API

Cosmos DB nabízí otevřený programovací model RESTful přes protokol HTTP. Model prostředků se skládá ze sady prostředků v rámci databázového účtu, který obsahuje podmínky pro předplatné Azure. Databázový účet se skládá ze sady *databází*, z nichž každá může obsahovat více *kontejnerů*, které zase obsahují *položky*, UDF a další typy prostředků. Každý prostředek Cosmos DB je adresovatelný pomocí logického a stabilního identifikátoru URI. Sada prostředků se nazývá *informační kanál*. 

Základní model interakce s těmito prostředky je prostřednictvím operací `GET`http, `PUT`, `POST`a `DELETE`, s jejich standardními interpretací. Použijte `POST` k vytvoření nového prostředku, spuštění uložené procedury nebo vystavení dotazu Cosmos DB. Dotazy jsou vždy operace jen pro čtení bez vedlejších účinků.

Následující příklady znázorňují `POST` dotaz rozhraní SQL API proti ukázkovým položkám. Dotaz má jednoduchý filtr na vlastnost JSON `name` . `x-ms-documentdb-isquery` Typ Content-Type: `application/query+json` Headers označuje, že operace je dotaz. Nahraďte `mysqlapicosmosdb.documents.azure.com:443` identifikátorem URI pro váš Cosmos DB účet.

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

Další složitější dotaz vrátí více výsledků spojení:

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

Pokud se výsledky dotazu nevejdou na jednu stránku, REST API vrátí token pokračování v hlavičce `x-ms-continuation-token` odpovědi. Klienti mohou stránkování výsledků zahrnutím hlavičky do následujících výsledků. Můžete také řídit počet výsledků na stránce přes hlavičku `x-ms-max-item-count` čísla.

Pokud má dotaz agregační funkci, jako je počet, stránka dotazu může vracet částečně agregovanou hodnotu jenom na jedné stránce výsledků. Klienti musí pomocí těchto výsledků provádět agregaci druhé úrovně, aby bylo možné vytvořit konečné výsledky. Například součet počtu vrácených na jednotlivých stránkách vrátí celkový počet.

Chcete-li spravovat zásady konzistence dat pro dotazy, `x-ms-consistency-level` použijte záhlaví jako ve všech požadavcích REST API. Konzistence relace také vyžaduje, aby se `x-ms-session-token` v žádosti o dotaz navracelo poslední záhlaví souboru cookie. Zásada indexování dotazovaného kontejneru může mít také vliv na konzistenci výsledků dotazu. S výchozími nastaveními zásad indexování pro kontejnery je index vždy aktuální s obsahem položky a výsledky dotazu odpovídají konzistenci zvolené pro data. Další informace najdete v tématu [Azure Cosmos DB úrovně konzistence] [úrovně konzistence].

Pokud nakonfigurované zásady indexování na kontejneru nepodporují zadaný dotaz, Azure Cosmos DB server vrátí 400 "Chybný požadavek". Tato chybová zpráva vrátí dotazy s cestami výslovně vyloučenými z indexování. Můžete zadat `x-ms-documentdb-query-enable-scan` hlavičku, která umožní dotazu provést kontrolu, pokud není k dispozici index.

Podrobné metriky pro provádění dotazů můžete získat nastavením `x-ms-documentdb-populatequerymetrics` záhlaví na. `true` Další informace najdete v tématu [metriky dotazů SQL pro Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (.NET SDK)

Sada .NET SDK podporuje dotazy LINQ i SQL. Následující příklad ukazuje, jak provést předchozí dotaz filtru s rozhraním .NET:

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

Následující příklad porovnává dvě vlastnosti pro rovnost v rámci každé položky a používá anonymní projekce.

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

Následující příklad ukazuje spojení vyjádřená pomocí LINQ `SelectMany`.

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

Klient rozhraní .NET automaticky projde všechny stránky výsledků dotazu v `foreach` blocích, jak je znázorněno v předchozím příkladu. Možnosti dotazu představené v části [REST API](#REST-API) jsou také k dispozici v sadě .NET SDK pomocí tříd `FeedOptions` a `FeedResponse` v `CreateDocumentQuery` metodě. Počet stránek můžete řídit pomocí `MaxItemCount` nastavení.

Můžete také explicitně řídit `IDocumentQueryable` stránkování vytvořením pomocí `IQueryable` objektu a potom přečtením `ResponseContinuationToken` hodnot a jejich předáním zpátky jako `RequestContinuationToken` v. `FeedOptions` Můžete nastavit `EnableScanInQuery` , aby se povolily kontroly, když není dotaz podporovaný nakonfigurovanou zásadou indexování. V případě kontejnerů rozdělených do oddílů `PartitionKey` můžete použít ke spuštění dotazu na jeden oddíl, i když ho Azure Cosmos DB může automaticky extrahovat z textu dotazu. Můžete použít `EnableCrossPartitionQuery` ke spouštění dotazů proti více oddílům.

Další ukázky .NET s dotazy najdete v [ukázkách Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3) v GitHubu.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>JavaScriptové rozhraní API na straně serveru

Azure Cosmos DB poskytuje programovací model pro spouštění logiky [aplikace založené na JavaScriptu](stored-procedures-triggers-udfs.md) přímo na kontejnerech, pomocí uložených procedur a triggerů. Logika jazyka JavaScript registrovaná na úrovni kontejneru může následně vystavovat operace databáze u položek daného kontejneru, zabalené v transakcích s KYSELINou v okolí.

Následující příklad ukazuje, jak používat `queryDocuments` v rozhraní API serveru JavaScript k vytváření dotazů z uvnitř uložených procedur a triggerů:

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
- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md)
