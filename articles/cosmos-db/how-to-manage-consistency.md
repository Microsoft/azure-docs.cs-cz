---
title: Správa konzistence v Azure Cosmos DB
description: Naučte se konfigurovat a spravovat úrovně konzistence v Azure Cosmos DB pomocí Azure Portal, sady .NET SDK, Java SDK a různých dalších sad SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/10/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 2b3433d969611fabe1b12a8dcabfe6e50066a8c1
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491185"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Správa úrovní konzistence ve službě Azure Cosmos DB

Tento článek vysvětluje, jak spravovat úrovně konzistence v Azure Cosmos DB. Naučíte se, jak nakonfigurovat výchozí úroveň konzistence, přepsat výchozí konzistenci, ručně spravovat tokeny relací a pochopit metriku služby PBS (probabilistically Bounded).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Konfigurace výchozí úrovně konzistence

[Výchozí úroveň konzistence](consistency-levels.md) je úroveň konzistence, kterou klienti používají ve výchozím nastavení.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Pokud chcete zobrazit nebo upravit výchozí úroveň konzistence, přihlaste se k Azure Portal. Vyhledejte účet Azure Cosmos a otevřete **výchozí podokno konzistence** . Vyberte požadovanou úroveň konzistence jako novou výchozí hodnotu a pak vyberte **Uložit**. Azure Portal také nabízí vizualizaci různých úrovní konzistence s hudebními poznámkami. 

:::image type="content" source="./media/how-to-manage-consistency/consistency-settings.png" alt-text="Nabídka konzistence v Azure Portal":::

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli)

Vytvořte účet Cosmos s konzistencí relací a pak aktualizujte výchozí konzistenci.

```azurecli
# Create a new account with Session consistency
az cosmosdb create --name $accountName --resource-group $resourceGroupName --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name $accountName --resource-group $resourceGroupName --default-consistency-level Strong
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Vytvořte účet Cosmos s konzistencí relací a pak aktualizujte výchozí konzistenci.

```azurepowershell-interactive
# Create a new account with Session consistency
New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Location $locations -Name $accountName -DefaultConsistencyLevel "Session"

# Update an existing account's default consistency
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Name $accountName -DefaultConsistencyLevel "Strong"
```

---

## <a name="override-the-default-consistency-level"></a>Přepsání výchozí úrovně konzistence

Klienti můžou přepsat výchozí úroveň konzistence nastavenou službou. Úroveň konzistence se dá nastavit pro každý požadavek, který přepíše výchozí úroveň konzistence nastavenou na úrovni účtu.

> [!TIP]
> Konzistenci je možné **zmírnit** jenom na úrovni žádosti. Pokud chcete přejít ze slabé na silnější konzistenci, aktualizujte výchozí konzistenci účtu Cosmos.

### <a name="net-sdk"></a><a id="override-default-consistency-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item,
        new PartitionKey(itemPartitionKey),
        requestOptions);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Sada Java v4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencyAsync)]

# <a name="sync"></a>[Synchronizovat](#tab/api-sync)

   Sada Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySync)]

--- 

### <a name="java-v2-sdks"></a><a id="override-default-consistency-javav2"></a> Sady SDK pro Java v2

# <a name="async"></a>[Async](#tab/api-async)

Async Java v2 SDK (Maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

# <a name="sync"></a>[Synchronizovat](#tab/api-sync)

Synchronizace sady Java v2 SDK (Maven com. Microsoft. Azure:: Azure-DocumentDB)

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a name="python-sdk"></a><a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Využití tokenů relace

Jednou z úrovní konzistence v Azure Cosmos DB je konzistence *relace* . Toto je výchozí úroveň, která se ve výchozím nastavení používá pro účty Cosmos. Při práci s konzistencí *relace* bude klient používat token relace interně s každou žádostí pro čtení/dotaz, aby bylo zajištěno, že se zachová nastavená úroveň konzistence.

Chcete-li spravovat tokeny relace ručně, Získejte token relace z odpovědi a nastavte je na požadavek. Pokud nepotřebujete spravovat tokeny relací ručně, nemusíte tyto ukázky používat. Sada SDK automaticky sleduje tokeny relací. Pokud nenastavíte token relace ručně, použije sada SDK nejnovější token relace.

### <a name="net-sdk"></a><a id="utilize-session-tokens-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Sada Java v4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencySessionAsync)]

# <a name="sync"></a>[Synchronizovat](#tab/api-sync)

   Sada Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySessionSync)]

--- 

### <a name="java-v2-sdks"></a><a id="utilize-session-tokens-javav2"></a>Sady SDK pro Java v2

# <a name="async"></a>[Async](#tab/api-async)

Async Java v2 SDK (Maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

# <a name="sync"></a>[Synchronizovat](#tab/api-sync)

Synchronizace sady Java v2 SDK (Maven com. Microsoft. Azure:: Azure-DocumentDB)

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a name="python-sdk"></a><a id="utilize-session-tokens-python"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Monitorování metriky Pravděpodobnostně omezená neaktuálnost (PBS)

Jak co má být konečná konzistence? V případě průměrného případu můžeme nabídnout neplatnost hranic s ohledem na historii a čas verzí. Metrika služby [**PBS (probabilistically Bounded)**](https://pbs.cs.berkeley.edu/) se pokusí vyčíslit pravděpodobnost neaktuálnosti a zobrazí ji jako metriku. Metriku služby PBS zobrazíte tak, že v Azure Portal přejdete na svůj účet Azure Cosmos. Otevřete podokno **metriky** a vyberte kartu **konzistence** . Podívejte se na graf s názvem **pravděpodobnost silně konzistentních čtení na základě vašich úloh (viz PBS)**.

:::image type="content" source="./media/how-to-manage-consistency/pbs-metric.png" alt-text="Nabídka konzistence v Azure Portal":::

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak spravovat konflikty dat, nebo přejděte k dalšímu klíčovému konceptu v Azure Cosmos DB. Viz následující články:

* [Úrovně konzistence v Azure Cosmos DB](consistency-levels.md)
* [Dělení a distribuce dat](./partitioning-overview.md)
* [Správa konfliktů mezi oblastmi](how-to-manage-conflicts.md)
* [Dělení a distribuce dat](partitioning-overview.md)
* [Návrh moderních systémů distribuovaných databází – kompromisy v konzistenci](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Vysoká dostupnost](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)