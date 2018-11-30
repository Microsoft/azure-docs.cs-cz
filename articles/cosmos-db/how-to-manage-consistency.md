---
title: Informace o správě konzistence ve službě Azure Cosmos DB
description: Informace o správě konzistence ve službě Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 9d8c1296fc811d97dc9e7e66ad9bd9fdc79d66f9
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634332"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Správa úrovní konzistence ve službě Azure Cosmos DB

Tento článek vysvětluje, jak spravovat úrovně konzistence ve službě Azure Cosmos DB. Zjistíte, jak nakonfigurovat výchozí úroveň konzistence, přepsat výchozí konzistence, ručně spravovat relace tokeny a pochopit metrika Probabilistically omezená Neaktuálnost (PBS).

## <a name="configure-the-default-consistency-level"></a>Konfigurace výchozí úrovně konzistence

Výchozí úroveň konzistence je úroveň konzistence, kterou klienti používají ve výchozím nastavení. Klienty můžete přepsat.

### <a name="cli"></a>Rozhraní příkazového řádku

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

Tento příklad vytvoří nový účet Azure Cosmos DB s několika hlavními uzly povolena v oblastech USA – východ a USA – západ. Výchozí zásady konzistence je nastavena jako relace.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="portal"></a>Portál

Pokud chcete zobrazit nebo upravit výchozí úroveň konzistence, přihlaste se k webu Azure portal. Vyhledejte svůj účet služby Azure Cosmos DB a otevřete **výchozí konzistence** podokně. Vyberte úroveň konzistence jako nové výchozí nastavení a potom vyberte **Uložit**.

![Konzistence nabídky na webu Azure Portal](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Přepsání výchozí úrovně konzistence

Klienti mohou přepsat výchozí úroveň konzistence, který je nastaven služba. Tuto možnost můžete nastavit pro celý klienta nebo na žádost.

### <a id="override-default-consistency-dotnet"></a>.NET SDK

```csharp
// Override consistency at the client level
ConsistencyPolicy consistencyPolicy = new ConsistencyPolicy
    {
        DefaultConsistencyLevel = ConsistencyLevel.BoundedStaleness,
        MaxStalenessIntervalInSeconds = 5,
        MaxStalenessPrefix = 100
    };
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, consistencyPolicy);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-java-async"></a>Java Async SDK

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

### <a id="override-default-consistency-java-sync"></a>Java Sync SDK

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Strong);
```

### <a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Strong
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Strong)
```

## <a name="utilize-session-tokens"></a>Využití tokenů relace

Tokeny relace spravovat ručně, získání tokenu relace z odpovědi a nastavit každý požadavek. Pokud není nutné ručně spravovat tokeny relace, není nutné používat tyto ukázky. Sada SDK uchovává informace o relaci tokeny automaticky. Pokud nenastavíte tokenu relace ručně, ve výchozím nastavení, sada SDK používá nejnovější tokenu relace.

### <a id="utilize-session-tokens-dotnet"></a>.NET SDK

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-java-async"></a>Java Async SDK

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

### <a id="utilize-session-tokens-java-sync"></a>Java Sync SDK

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>Python SDK

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

Pokud chcete zobrazit metriky PBS, přejděte ke svému účtu Azure Cosmos DB na webu Azure Portal. Otevřít **metriky** podokně a vyberte **konzistence** kartu. Podívejte se na grafu s názvem **pravděpodobnost silně konzistentních čtení na základě vašich úloh (viz PBS)**.

![Graf PBS na webu Azure Portal](./media/how-to-manage-consistency/pbs-metric.png)

Pomocí nabídky metriky služby Azure Cosmos DB najdete v článku tuto metriku. Nezobrazí se v prostředí Azure Monitoring metrik.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak spravovat konfliktů v datech, nebo přejít k další klíčovým konceptem ve službě Azure Cosmos DB. Viz následující články:

* [Správa konfliktů mezi oblastmi](how-to-manage-conflicts.md)
* [Dělení a distribuce dat](partition-data.md)
