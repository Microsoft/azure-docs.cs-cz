---
title: Použití tokenů prostředků Azure Cosmos DB se sadou Gremlin SDK
description: Zjistěte, jak vytvořit tokeny prostředků a použít je pro přístup k databázi grafu.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 42f3c7f3351bddab429489dccf28587549d76e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897852"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Použití tokenů prostředků Azure Cosmos DB se sadou Gremlin SDK

Tento článek vysvětluje, jak používat [tokeny prostředků Azure Cosmos DB](secure-access-to-data.md) pro přístup k databázi graphu prostřednictvím sady Gremlin SDK.

## <a name="create-a-resource-token"></a>Vytvoření tokenu prostředku

Sada Apache TinkerPop Gremlin SDK nemá rozhraní API, které by bylo třeba použít k vytvoření tokenů prostředků. Token *termínu prostředek* je koncept Azure Cosmos DB. Chcete-li vytvořit tokeny prostředků, stáhněte si [sadu Azure Cosmos DB SDK](sql-api-sdk-dotnet.md). Pokud vaše aplikace potřebuje vytvořit tokeny prostředků a použít je pro přístup k databázi grafu, vyžaduje dvě samostatné sady SDK.

Hierarchie objektového modelu nad tokeny prostředků je znázorněna v následujícím přehledu:

- **Účet Azure Cosmos DB** – entita nejvyšší úrovně, ke `contoso.gremlin.cosmos.azure.com`které je přidružena služba DNS (například ).
  - **Databáze Azure Cosmos DB**
    - **Uživatel**
      - **Oprávnění**
        - **Token** - Vlastnost objektu Permission, která označuje, jaké akce jsou povoleny nebo odepřeny.

Token prostředku používá následující `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`formát: . Tento řetězec je neprůhledný pro klienty a měl by být používán tak, jak je, bez úprav nebo interpretace.

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Použití tokenu prostředku
Tokeny prostředků můžete použít přímo jako vlastnost "heslo" při vytváření třídy GremlinServer.

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a master key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Stejný přístup funguje ve všech sadách TinkerPop Gremlin SDK.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Omezení

S jedním účtem Gremlin můžete vydat neomezený počet žetonů. Můžete však použít pouze až 100 tokenů současně během 1 hodiny. Pokud aplikace překročí limit tokenu za hodinu, je požadavek na ověření odepřen a zobrazí se následující chybová zpráva: "Překročen limit tokenu prostředků 100, který lze použít současně." Nefunguje zavřít aktivní připojení, které používají konkrétní tokeny k uvolnění slotů pro nové tokeny. Databázový stroj Azure Cosmos DB Gremlin sleduje jedinečné tokeny během hodiny bezprostředně před požadavkem na ověření.

## <a name="permission"></a>Oprávnění

Obvyklá chyba, že aplikace dojít při jejich použití tokeny prostředků je "Nedostatečná oprávnění k dispozici v hlavičce autorizace pro odpovídající požadavek. Opakujte akci s jinou hlavičkou autorizace." Tato chyba je vrácena, když gremlin traversal pokusí o zápis hrany nebo vrcholu, ale token prostředku uděluje pouze oprávnění *ke čtení.* Zkontrolujte, zda prochází, zda obsahuje některý z následujících kroků: *.addV()*, *.addE()*, *.drop()* nebo *.property()*.

## <a name="next-steps"></a>Další kroky
* [Řízení přístupu na základě rolí](role-based-access-control.md) v Azure Cosmos DB
* [Zjistěte, jak zabezpečit přístup k datům](secure-access-to-data.md) v Azure Cosmos DB
