---
title: Azure Cosmos DB tokeny prostředků pomocí Gremlin
description: Naučte se vytvářet tokeny prostředků a používat je pro přístup k databázi grafu.
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.author: olignat
ms.openlocfilehash: fcb18fb14cf787713735da07ca2048d0853fa46c
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806908"
---
# <a name="azure-cosmos-db-resource-tokens-with-gremlin"></a>Azure Cosmos DB tokeny prostředků pomocí Gremlin
Tento článek vysvětluje, jak používat [tokeny prostředků Cosmos DB](secure-access-to-data.md) pro přístup k databázi grafu prostřednictvím sady SDK Gremlin.

## <a name="create-a-resource-token"></a>Vytvoření tokenu prostředku

Sada TinkerPop Gremlin SDK nemá rozhraní API k vytváření tokenů prostředků. Token prostředku je Cosmos DB koncept. Pokud chcete vytvořit tokeny prostředků, Stáhněte si [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md). Pokud vaše aplikace potřebuje vytvořit tokeny prostředků a používat je pro přístup k databázi grafu, potřebuje 2 samostatné sady SDK.

Hierarchie objektového modelu nad tokeny prostředků:
- **Cosmos DB účet** – entita nejvyšší úrovně, ke které je přidružená služba DNS, například`contoso.gremlin.cosmos.azure.com`
  - **Cosmos DB databáze**
    - **Uživatelský**
      - **Udělen**
        - *Token* – vlastnost objektu **oprávnění** , která označuje, jaké akce jsou povolené nebo odepřené.

Token prostředku má formát `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`. Tento řetězec je neprůhledný pro klienty a měl by být použit tak, jak je bez úprav nebo výkladu.

```csharp
// Notice that document client is created against .NET SDK end-point rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // Token will not be returned during ReadPermissionReedAsync() call.
  // This call will succeed only if database id, user id and permission id already exist. 
  // Note that <database id> is not a database name, it is a base64 string that represents database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Použít token prostředku
Tokeny prostředků lze použít přímo jako vlastnost "Password" při vytváření `GremlinServer` třídy.

```csharp
// Gremlin application needs to be given a resource token. It can't discover the token on its own.
// Token can be obtained for a given permission using Cosmos DB SDK or passed into the application as command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure gremlin servier to use resource token rather than master key
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Stejný přístup funguje ve všech sadách SDK TinkerPop Gremlin.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Omezení

Jeden účet Gremlin může vystavovat neomezený počet tokenů, ale v průběhu **1 hodiny**se dá současně použít jenom tokeny **100** . Pokud aplikace překračuje limit počtu tokenů za hodinu, bude žádost o ověření odepřena `"Exceeded allowed resource token limit of 100 that can be used concurrently"`s chybovou zprávou. Uzavírání aktivních připojení s konkrétními tokeny pro uvolnění slotů pro nové tokeny nebude ovocné. Cosmos DB databázový stroj Gremlin sleduje jedinečné tokeny za poslední hodinu před požadavkem na ověření.

## <a name="permission"></a>Oprávnění

Běžné chybové aplikace přicházejí v průběhu používání tokenů `"Insufficient permissions provided in the authorization header for the corresponding request. Please retry with another authorization header."`prostředků. Tato chyba se vrátí, když se Gremlin pokusy o zápis okraje nebo vrcholu, ale token prostředku uděluje `Read` jenom oprávnění. Zkontrolujte, zda obsahuje některý z následujících kroků `.addV()`:, `.addE()`, `.drop()`nebo `.property()`.

## <a name="next-steps"></a>Další postup
* [Řízení přístupu na základě role](role-based-access-control.md) v Azure Cosmos DB
* [Naučte se zabezpečit přístup k datům](secure-access-to-data.md) v Azure Cosmos DB
