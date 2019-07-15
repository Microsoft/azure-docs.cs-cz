---
title: Jak implementovat vlastní synchronizaci pro optimalizaci pro vyšší dostupnost a výkon ve službě Azure Cosmos DB
description: Zjistěte, jak implementovat vlastní synchronizaci pro optimalizaci pro vyšší dostupnost a výkon ve službě Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: de66149a2ea3e01e62aa8e33ea5a99121a21524f
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986087"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Implementace vlastního synchronizace pro optimalizaci pro vyšší dostupnost a výkon

Azure Cosmos DB nabízí [pět jasně definovaných úrovní konzistence](consistency-levels.md) můžete zvolit z vyvážit kompromis mezi konzistencí, výkonu a dostupnosti. Silná konzistence pomáhá zajistit, že data synchronně replikuje a trvale uchovávaných v každé oblasti, kde je k dispozici účet Azure Cosmos. Tato konfigurace poskytuje nejvyšší úroveň odolnosti, ale je za cenu výkonu a dostupnosti. Pokud chcete, aby vaše aplikace řídit nebo uvolnit delší trvanlivost dat tak, aby odpovídala aplikace potřebuje bez narušení dostupnosti, můžete použít *vlastní synchronizaci* v aplikační vrstvě pro dosažení úroveň odolnosti je Chcete.

Následující obrázek znázorňuje vizuálně vlastní synchronizační model:

![Vlastní synchronizace](./media/how-to-custom-synchronization/custom-synchronization.png)

V tomto scénáři je kontejner služby Azure Cosmos globálně replikovat napříč několika oblastmi na více kontinentech. Pomocí silnou konzistenci pro všechny oblasti v tomto scénáři má vliv na výkon. K zajištění vyšší úroveň odolnosti dat, bez negativního vlivu latence zápisu, může aplikace použít dva klienti, které sdílejí stejný [tokenu relace](how-to-manage-consistency.md#utilize-session-tokens).

První klient může zapisovat data do místní oblast (například USA – západ). Čtení klienta, který se používá k zajištění synchronizace je druhý klient (například v oblasti USA – východ). O toku tokenu relace ze zápisu odpovědi na následující čtení, čtení zajišťuje synchronizaci zápisy do oblasti USA – východ. Azure Cosmos DB zajišťuje, že zápisy jsou vidět alespoň jedné oblasti. Jejich zaručeno, že k oblastnímu výpadku zvládnout situaci, kdy původní oblast pro zápis ocitne mimo provoz. V tomto scénáři při každém zápisu synchronizované do oblasti USA – východ, snižuje tak latenci využívající silnou konzistenci napříč všemi oblastmi. V případě několika hlavními databázemi, kde se objeví zápisy v každé oblasti, můžete rozšířit tento model se synchronizovat do více oblastí současně.

## <a name="configure-the-clients"></a>Konfigurace klientů

Následující příklad ukazuje vrstvy přístupu k datům, který vytvoří instanci dvěma klienty pro vlastní synchronizaci:

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
        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
    }
}
```

## <a name="implement-custom-synchronization"></a>Implementace vlastní synchronizace

Po inicializaci klienti jsou aplikace můžete provádět zápisy do místní oblast (oblasti USA – západ) a vynucení synchronizace zápisy do oblasti USA – východ následujícím způsobem.

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

Můžete rozšířit modelu synchronizovat do více oblastí současně.

## <a name="next-steps"></a>Další postup

Další informace o globální distribuci a konzistence ve službě Azure Cosmos DB, přečtěte si tyto články:

* [Vyberte úroveň správné konzistence ve službě Azure Cosmos DB](consistency-levels-choosing.md)
* [Kompromisy konzistencí, dostupností a výkonem ve službě Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Správa konzistence ve službě Azure Cosmos DB](how-to-manage-consistency.md)
* [Distribuce dělení a data ve službě Azure Cosmos DB](partition-data.md)
