---
title: Místní koncové body pro databázi Azure Cosmos DB Graph
description: Zjistěte, jak se připojit k nejbližšímu koncovému bodu databáze grafu pro vaši aplikaci.
author: jasonwhowell
ms.author: jasonh
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/09/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 5537b70f9852f5b5a17362c13e2c9b8e8e9fc43c
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570617"
---
# <a name="regional-endpoints-for-azure-cosmos-db-graph-account"></a>Regionální koncové body pro účet Azure Cosmos DB Graph
Databáze Azure Cosmos DB Graph je [globálně distribuovaná](distribute-data-globally.md) , takže aplikace můžou používat víc koncových bodů pro čtení. Aplikace, které potřebují oprávnění k zápisu ve více umístěních, by měly povolit možnost [zápisu ve více oblastech](how-to-multi-master.md) .

Důvody pro výběr více než jedné oblasti:
1. **Horizontální škálovatelnost čtení** – jelikož zatížení aplikace se zvyšuje, může být obezřetné směrovat provoz čtení do různých oblastí Azure.
2. **Nižší latence** – můžete snížit nároky na latenci sítě u každého průchodu směrováním provozu čtení a zápisu do nejbližší oblasti Azure.

Požadavek na zaplnění **dat** se dosáhne nastavením Azure Resource Manager zásady na účet Cosmos DB. Zákazník může omezit oblasti, do kterých Cosmos DB replikují data.

## <a name="traffic-routing"></a>Směrování provozu

Databázový stroj Cosmos DBového grafu je spuštěný v několika oblastech, z nichž každý obsahuje více clusterů. Každý cluster má stovky počítačů. Cosmos DB grafu účtu DNS CNAME *AccountName.Gremlin.Cosmos.Azure.com* se přeloží na záznam DNS clusteru. Jedna IP adresa nástroje pro vyrovnávání zatížení skrývá interní topologii clusteru.

Pro každou oblast účtu Cosmos DB Graph se vytvoří místní záznam DNS CNAME. Formát místního koncového bodu je *AccountName-region.Gremlin.Cosmos.Azure.com*. Segment oblasti regionálního koncového bodu se získá odebráním všech mezer z názvu [oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions) . Například `"East US 2"` oblast pro `"contoso"` účet globální databáze by měla *Contoso-eastus2.Gremlin.Cosmos.Azure.com* DNS CNAME

Klient TinkerPop Gremlin je navržený tak, aby fungoval s jediným serverem. Aplikace může pro přenos dat pro čtení a zápis použít globální zapisovatelný záznam DNS. Aplikace s podporou oblastí by měly pro čtení provozu používat místní koncový bod. Použijte místní koncový bod pro zápis do provozu pouze v případě, že je konkrétní oblast nakonfigurována pro příjem zápisu. 

> [!NOTE]
> Modul Cosmos DB Graph může přijmout operaci zápisu v oblasti čtení, protože provoz proxy serveru do oblasti zápisu. Nedoporučujeme odesílat zápisy do oblasti jen pro čtení, protože se zvyšuje latence procházení a podléhá omezením v budoucnosti.

Záznam CNAME globálního databázového účtu vždy odkazuje na platnou oblast zápisu. Během převzetí služeb při selhání na straně serveru Cosmos DB aktualizuje globální účet databáze CNAME tak, aby odkazoval na novou oblast. Pokud aplikace nemůže po převzetí služeb při selhání zpracovat přesměrování provozu, měl by použít globální účet služby DNS CNAME účtu databáze.

> [!NOTE]
> Cosmos DB nesměruje provoz na základě geografické blízkosti volajícího. Pro výběr správné oblasti podle jedinečných potřeb aplikace je k diskaždé aplikaci.

## <a name="portal-endpoint-discovery"></a>Zjišťování koncových bodů portálu

Nejjednodušší způsob, jak získat seznam oblastí pro účet Azure Cosmos DB Graph, je okno Přehled v Azure Portal. Bude fungovat pro aplikace, které často nemění oblasti, nebo mají způsob, jak seznam aktualizovat prostřednictvím konfigurace aplikace.

:::image type="content" source="./media/how-to-use-regional-gremlin/get-end-point-portal.png " alt-text="Načtení oblastí účtu Cosmos DB Graph z portálu":::

Níže uvedený příklad ukazuje obecné principy přístupu k místnímu Gremlin koncovému bodu. Aplikace by měla zvážit počet oblastí pro odeslání provozu do a počet odpovídajících klientů Gremlin a jejich vytvoření.

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

## <a name="sdk-endpoint-discovery"></a>Zjišťování koncových bodů sady SDK

Aplikace může použít [sadu Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) ke zjištění umístění pro čtení a zápis pro účet grafu. Tato umístění se můžou kdykoli změnit ruční změnou konfigurace na straně serveru nebo na automatické převzetí služeb při selhání.

Sada TinkerPop Gremlin SDK nemá rozhraní API pro zjišťování oblastí účtů databázového grafu Cosmos DB. Aplikace, které potřebují ke zjišťování koncových bodů za běhu, musí v prostoru procesu hostovat 2 samostatné sady SDK.

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
* [Správa řízení databázových účtů](how-to-manage-database-account.md) v Azure Cosmos DB
* [Vysoká dostupnost](high-availability.md) v Azure Cosmos DB
* [Globální distribuce s Azure Cosmos DB – pod kapotou](global-dist-under-the-hood.md)
* [Ukázky v Azure CLI](cli-samples.md) pro Azure Cosmos DB
