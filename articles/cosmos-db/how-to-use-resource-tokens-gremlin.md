---
title: Použití Azure Cosmos DBch tokenů prostředků v sadě SDK pro Gremlin
description: Naučte se vytvářet tokeny prostředků a používat je pro přístup k databázi grafu.
author: jasonwhowell
ms.author: jasonh
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/06/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 45771f16b7587392a68ea88a05467fc4f30079c8
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2020
ms.locfileid: "91408939"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Použití Azure Cosmos DBch tokenů prostředků v sadě SDK pro Gremlin

Tento článek vysvětluje, jak používat [tokeny prostředků Azure Cosmos DB](secure-access-to-data.md) pro přístup k databázi grafu prostřednictvím sady SDK pro Gremlin.

## <a name="create-a-resource-token"></a>Vytvoření tokenu prostředku

Sada Apache TinkerPop Gremlin SDK nemá rozhraní API, které by bylo možné použít k vytváření tokenů prostředků. Pojem *token prostředku* je Azure Cosmos DB koncept. Pokud chcete vytvořit tokeny prostředků, Stáhněte si [sadu Azure Cosmos DB SDK](sql-api-sdk-dotnet.md). Pokud vaše aplikace potřebuje vytvořit tokeny prostředků a používat je pro přístup k databázi grafu, vyžaduje dvě samostatné sady SDK.

Hierarchie objektového modelu nad tokeny prostředků je znázorněná v následujícím přehledu:

- **Azure Cosmos DB účet** – entita nejvyšší úrovně, která má k sobě PŘIDRUŽENOU službu DNS (například `contoso.gremlin.cosmos.azure.com` ).
  - **Azure Cosmos DB databáze**
    - **Uživatel**
      - **Oprávnění**
        - **Token** – vlastnost objektu oprávnění, která označuje, které akce jsou povoleny nebo odepřeny.

Token prostředku používá následující formát: `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"` . Tento řetězec je neprůhledný pro klienty a měl by se používat tak, jak je, bez úprav nebo výkladu.

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

## <a name="use-a-resource-token"></a>Použít token prostředku
Tokeny prostředků můžete použít přímo jako vlastnost "Password" při vytváření třídy GremlinServer.

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

Stejný přístup funguje ve všech sadách SDK TinkerPop Gremlin.

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

S jedním účtem Gremlin můžete vystavit neomezený počet tokenů. V průběhu jedné hodiny ale můžete použít až 100 tokenů současně. Pokud aplikace překročí limit tokenu za hodinu, je žádost o ověření zamítnutá a zobrazí se následující chybová zpráva: "překročení povoleného limitu tokenu prostředku 100, který se dá použít souběžně." Nefunguje na ukončení aktivních připojení, která používají konkrétní tokeny k uvolnění slotů pro nové tokeny. Databázový stroj Azure Cosmos DB Gremlin sleduje jedinečné tokeny během hodiny bezprostředně před požadavkem na ověření.

## <a name="permission"></a>Oprávnění

Častá chyba, kterou aplikace, ke kterým dochází, když používají tokeny prostředků, jsou v autorizační hlavičce pro odpovídající požadavek nedostatečná oprávnění. Zkuste to prosím znovu s jinou autorizační hlavičkou. " Tato chyba se vrátí, když se Gremlin přecházení pokusí zapsat Edge nebo vrchol, ale token prostředku uděluje pouze oprávnění *ke čtení* . Zkontrolujte svůj průchod, abyste viděli, zda obsahuje některý z následujících kroků: *. addV ()*, *. addE ()*, *. drop ()* nebo *. Property ()*.

## <a name="next-steps"></a>Další kroky
* [Řízení přístupu na základě role v Azure (Azure RBAC)](role-based-access-control.md) v Azure Cosmos DB
* [Naučte se zabezpečit přístup k datům](secure-access-to-data.md) v Azure Cosmos DB
