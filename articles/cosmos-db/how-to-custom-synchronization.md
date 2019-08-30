---
title: Implementace vlastní synchronizace pro optimalizaci pro zajištění vyšší dostupnosti a výkonu v Azure Cosmos DB
description: Naučte se implementovat vlastní synchronizaci k optimalizaci pro zajištění vyšší dostupnosti a výkonu v Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: 8fce14496b9f8fa17f2dbfd04b7ea42f1495a8a9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093341"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Implementace vlastní synchronizace pro optimalizaci pro zajištění vyšší dostupnosti a výkonu

Azure Cosmos DB nabízí [pět jasně definovaných úrovní konzistence](consistency-levels.md) , ze kterých si můžete vybrat, abyste si vyrovnali kompromisy mezi konzistencí, výkonem a dostupností. Silná konzistence pomáhá zajistit, aby data byla synchronně replikována a trvale trvalá v každé oblasti, kde je dostupný účet Azure Cosmos. Tato konfigurace poskytuje nejvyšší úroveň trvanlivosti, ale přináší náklady na výkon a dostupnost. Pokud chcete, aby vaše aplikace měla kontrolu nebo zmírnit odolnost dat, aby vyhovovala potřebám aplikace bez narušení dostupnosti, můžete použít *vlastní synchronizaci* na vrstvě aplikace, abyste dosáhli úrovně trvanlivosti, kterou požadujete.

Následující obrázek vizuálně znázorňuje vlastní synchronizační model:

![Vlastní synchronizace](./media/how-to-custom-synchronization/custom-synchronization.png)

V tomto scénáři se kontejner Azure Cosmos replikuje globálně napříč několika oblastmi na více kontinentech. Použití silné konzistence pro všechny oblasti v tomto scénáři má vliv na výkon. Aby se zajistila vyšší úroveň odolnosti dat bez narušení latence zápisu, může aplikace použít dva klienty, kteří sdílejí stejný [token relace](how-to-manage-consistency.md#utilize-session-tokens).

První klient může zapisovat data do místní oblasti (například USA – západ). Druhý klient (například v USA – východ) je klient pro čtení, který se používá k zajištění synchronizace. Výsledkem přetečení tokenu relace z odpovědi zápisu na následující čtení zajišťuje synchronizaci zápisů do USA – východ. Azure Cosmos DB zajišťuje, aby se zápisy zobrazily alespoň v jedné oblasti. Je zaručeno, že dojde k místnímu výpadku, pokud se původní oblast zápisu neukončí. V tomto scénáři je každý zápis synchronizovaný USA – východ, což snižuje latenci využívání silné konzistence napříč všemi oblastmi. V případě více hlavních scénářů, kde se zápisy vyskytují v každé oblasti, můžete tento model roztáhnout tak, aby paralelně synchronizoval více oblastí.

## <a name="configure-the-clients"></a>Konfigurace klientů

Následující příklad ukazuje vrstvu přístupu k datům, která vytváří instance dvou klientů pro vlastní synchronizaci:

### <a name="net-v2-sdk"></a>.Net V2 SDK
```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

### <a name="net-v3-sdk"></a>.Net V3 SDK
```csharp
class MyDataAccessLayer
{
    private CosmosClient writeClient;
    private CosmosClient readClient;

    public void InitializeAsync(string accountEndpoint, string key)
    {
        CosmosClientOptions writeConnectionOptions = new CosmosClientOptions();
        writeConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        writeConnectionOptions.ApplicationRegion = "West US";

        CosmosClientOptions readConnectionOptions = new CosmosClientOptions();
        readConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        readConnectionOptions.ApplicationRegion = "East US";


        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
        readClient = new CosmosClient(accountEndpoint, key, readConnectionOptions);
    }
}
```

## <a name="implement-custom-synchronization"></a>Implementace vlastní synchronizace

Po inicializaci klientů aplikace může provádět zápisy do místní oblasti (USA – západ) a vynuceně-synchronizovat zápisy do USA – východ následujícím způsobem.

### <a name="net-v2-sdk"></a>.Net V2 SDK
```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

### <a name="net-v3-sdk"></a>.Net V3 SDK
```csharp
class MyDataAccessLayer
{
     public async Task CreateItem(string databaseId, string containerId, dynamic item)
     {
        ItemResponse<dynamic> response = await writeClient.GetContainer("containerId", "databaseId")
            .CreateItemAsync<dynamic>(
                item,
                new PartitionKey("test"));

        await readClient.GetContainer("containerId", "databaseId").ReadItemAsync<dynamic>(
            response.Resource.id,
            new PartitionKey("test"),
            new ItemRequestOptions { SessionToken = response.Headers.Session, ConsistencyLevel = ConsistencyLevel.Session });
    }
}
```

Model můžete roztáhnout tak, aby se souběžně synchronizoval s více oblastmi.

## <a name="next-steps"></a>Další postup

Pokud chcete získat další informace o globální distribuci a konzistenci v Azure Cosmos DB, přečtěte si tyto články:

* [Vyberte správnou úroveň konzistence v Azure Cosmos DB](consistency-levels-choosing.md)
* [Kompromisy konzistence, dostupnosti a výkonu v Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Správa konzistence v Azure Cosmos DB](how-to-manage-consistency.md)
* [Dělení a distribuce dat v Azure Cosmos DB](partition-data.md)
