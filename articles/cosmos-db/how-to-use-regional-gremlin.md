---
title: Místní koncové body pro databázi Azure Cosmos DB Graph
description: Přečtěte si, jak se připojit k nejbližšímu koncovému bodu databáze Graph u vaší aplikace.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/09/2019
ms.openlocfilehash: 7aa1e0aa6bbbee9d40eb0d48318a8e2908a75f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897865"
---
# <a name="regional-endpoints-for-azure-cosmos-db-graph-account"></a>Místní koncové body pro účet Azure Cosmos DB Graph
Databáze Azure Cosmos DB Graph je [globálně distribuovaná,](distribute-data-globally.md) takže aplikace můžou používat více koncových bodů pro čtení. Aplikace, které potřebují přístup pro zápis na více místech, by [měly](how-to-multi-master.md) povolit funkci více hlavních serverů.

Důvody, proč si vybrat více než jednu oblast:
1. **Škálovatelnost pro horizontální čtení** – jako zatížení aplikace zvyšuje může být rozumné směrovat přenos čtení do různých oblastí Azure.
2. **Nižší latence** – můžete snížit latenci sítě režii každého průchodu směrováním čtení a zápisu provozu do nejbližší oblasti Azure.

Požadavek **na rezidenci dat** je dosažen nastavením zásad Azure Resource Manager u účtu Cosmos DB. Zákazník může omezit oblasti, do kterých Cosmos DB replikuje data.

## <a name="traffic-routing"></a>Směrování provozu

Databázový stroj Cosmos DB Graph běží ve více oblastech, z nichž každá obsahuje více clusterů. Každý cluster má stovky počítačů. Účet DNS CNAME služby Cosmos DB Graph *accountname.gremlin.cosmos.azure.com* překládá na DNS Záznam clusteru. Jedna IP adresa vyvažovače zatížení skryje interní topologii clusteru.

Pro každou oblast účtu Cosmos DB Graph je vytvořen místní záznam Dns CNAME. Formát místního koncového bodu je *accountname-region.gremlin.cosmos.azure.com*. Segment oblasti místní koncový bod se získá odebráním všech prostorů z názvu [oblasti Azure.](https://azure.microsoft.com/global-infrastructure/regions) Například `"East US 2"` oblast `"contoso"` pro globální databázový účet by měla *contoso-eastus2.gremlin.cosmos.azure.com* CNAME DNS

Klient TinkerPop Gremlin je navržen pro práci s jedním serverem. Aplikace může používat globální zapisovatelný DNS CNAME pro čtení a zápis provozu. Aplikace podporující oblast by měly používat místní koncový bod pro čtení provozu. Místní koncový bod použijte pro přenos zápisu pouze v případě, že je konkrétní oblast nakonfigurována tak, aby přijímala zápisy. 

> [!NOTE]
> Modul Cosmos DB Graph může přijmout operaci zápisu v oblasti čtení proxy přenospro zápis oblasti. Nedoporučuje se odesílat zápisy do oblasti jen pro čtení, protože zvyšuje latenci průchodu a v budoucnu podléhá omezením.

Globální databázový účet CNAME vždy odkazuje na platnou oblast zápisu. Během převzetí služeb při selhání oblasti zápisu na straně serveru Cosmos DB aktualizuje globální databázový účet CNAME tak, aby přešel na novou oblast. Pokud aplikace nemůže zpracovat přesměrování přenosů po převzetí služeb při selhání, měla by použít účet DNS CNAME globálního databázového účtu.

> [!NOTE]
> Cosmos DB nesměruje provoz na základě geografické blízkosti volajícího. Je na každé aplikaci, aby vybrala správnou oblast podle jedinečných potřeb aplikace.

## <a name="portal-endpoint-discovery"></a>Zjišťování koncového bodu portálu

Nejjednodušší způsob, jak získat seznam oblastí pro účet Azure Cosmos DB Graph, je přehled na webu Azure Portal. Bude fungovat pro aplikace, které často nemění oblasti nebo mají způsob, jak aktualizovat seznam pomocí konfigurace aplikace.

![Načtení oblastí účtu Cosmos DB Graph z portálu](./media/how-to-use-regional-gremlin/get-end-point-portal.png )

Příklad níže ukazuje obecné zásady přístupu k regionálnígremlin koncový bod. Aplikace by měla zvážit počet oblastí, které mají odeslat provoz a počet odpovídajících klientů Gremlin k vytvoření instance.

```csharp
// Example value: Central US, West US and UK West. This can be found in the overview blade of you Azure Cosmos DB Gremlin Account. 
// Look for Write Locations in the overview blade. You can click to copy and paste.
string[] gremlinAccountRegions = new string[] {"Central US", "West US" ,"UK West"};
string gremlinAccountName = "PUT-COSMOSDB-ACCOUNT-NAME-HERE";
string gremlinAccountKey = "PUT-ACCOUNT-KEY-HERE";
string databaseName = "PUT-DATABASE-NAME-HERE";
string graphName = "PUT-GRAPH-NAME-HERE";

foreach (string gremlinAccountRegion in gremlinAccountRegions)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = $"{gremlinAccountName}-{gremlinAccountRegion.ToLowerInvariant().Replace(" ", string.Empty)}.gremlin.cosmos.azure.com";

  GremlinServer regionalGremlinServer = new GremlinServer(
    hostname: regionalGremlinEndPoint, 
    port: 443,
    enableSsl: true,
    username: "/dbs/" + databaseName + "/colls/" + graphName,
    password: gremlinAccountKey);

  GremlinClient regionalGremlinClient = new GremlinClient(
    gremlinServer: regionalGremlinServer,
    graphSONReader: new GraphSON2Reader(),
    graphSONWriter: new GraphSON2Writer(),
    mimeType: GremlinClient.GraphSON2MimeType);
}
```

## <a name="sdk-endpoint-discovery"></a>Zjišťování koncového bodu sady SDK

Aplikace můžete použít [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) ke zjišťování umístění pro čtení a zápis pro účet Graph. Tato umístění lze kdykoli změnit pomocí ruční rekonfigurace na straně serveru nebo automatického převzetí služeb při selhání.

TinkerPop Gremlin SDK nemá rozhraní API pro zjišťování oblastí účtu databáze Cosmos DB Graph. Aplikace, které potřebují zjišťování koncového bodu modulu runtime, musí být v prostoru procesu hostovány 2 samostatné sady SDK.

```csharp
// Depending on the version and the language of the SDK (.NET vs Java vs Python)
// the API to get readLocations and writeLocations may vary.
IDocumentClient documentClient = new DocumentClient(
    new Uri(cosmosUrl),
    cosmosPrimaryKey,
    connectionPolicy,
    consistencyLevel);

DatabaseAccount databaseAccount = await cosmosClient.GetDatabaseAccountAsync();

IEnumerable<DatabaseAccountLocation> writeLocations = databaseAccount.WritableLocations;
IEnumerable<DatabaseAccountLocation> readLocations = databaseAccount.ReadableLocations;

// Pick write or read locations to construct regional endpoints for.
foreach (string location in readLocations)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = location
    .Replace("http:\/\/", string.Empty)
    .Replace("documents.azure.com:443/", "gremlin.cosmos.azure.com");
  
  // Use code from the previous sample to instantiate Gremlin client.
}
```

## <a name="next-steps"></a>Další kroky
* [Jak spravovat řízení databázových účtů](how-to-manage-database-account.md) v Azure Cosmos DB
* [Vysoká dostupnost](high-availability.md) v Azure Cosmos DB
* [Globální distribuce s Azure Cosmos DB – pod kapotou](global-dist-under-the-hood.md)
* [Ukázky azure cli](cli-samples.md) pro Azure Cosmos DB
