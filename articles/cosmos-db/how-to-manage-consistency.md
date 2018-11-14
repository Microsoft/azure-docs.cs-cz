---
title: Informace o správě konzistence ve službě Azure Cosmos DB
description: Informace o správě konzistence ve službě Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: be2c68922221af848c9e484d03527d02808c071a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283803"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Správa úrovní konzistence ve službě Azure Cosmos DB

Tento článek vysvětluje různé způsoby, jak nastavit výchozí konzistenci, přepsat tuto konzistenci na klientovi a ručně spravovat tokeny relace, a vysvětluje metriku Pravděpodobnostně omezená neaktuálnost (PBS).

## <a name="configure-the-default-consistency-level"></a>Konfigurace výchozí úrovně konzistence

Výchozí úroveň konzistence je úroveň konzistence, kterou budou klienti používat ve výchozím nastavení. Klienti ji můžou přepsat.

### <a name="cli"></a>Rozhraní příkazového řádku

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

Následující příklad vytvoří nový účet služby Cosmos DB s povolenou architekturou multi-master v oblastech USA – východ a USA – západ a nastavenou výchozí zásadou konzistence Omezená neaktuálnost s maximálním intervalem neaktuálnosti 10 sekund a maximálním počtem 200 tolerovaných zastaralých požadavků.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

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

Pokud chcete zobrazit nebo upravit výchozí úroveň konzistence, přihlaste se k webu Azure Portal. Vyhledejte svůj účet služby Cosmos DB a otevřete podokno **Výchozí konzistence**. Tady zvolte úroveň konzistence, kterou chcete nastavit jako novou výchozí úroveň, a pak klikněte na Uložit.

![Obrázek nabídky konzistence na webu Azure Portal](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Přepsání výchozí úrovně konzistence

Klienti můžou přepsat výchozí úroveň konzistence nastavenou službou. To je možné provést pro celého klienta nebo pro jednotlivé požadavky.

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

Pokud chcete ručně spravovat tokeny relace, můžete je získat z odpovědí a nastavit pro jednotlivé požadavky. Pokud nepotřebujete ručně spravovat tokeny relace, následující ukázky využívat nemusíte. Sada SDK bude automaticky sledovat tokeny relace, a pokud sami nenastavíte token relace, použije nejnovější token relace.

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

Pokud chcete zobrazit metriku PBS, na webu Azure Portal přejděte ke svému účtu služby Cosmos DB a otevřete podokno **Metriky**. Tady klikněte na kartu **Konzistence** a prohlédněte si graf **Pravděpodobnost silně konzistentních čtení na základě probíhající úlohy (viz PBS)**.

![Obrázek grafu PBS na webu Azure Portal](./media/how-to-manage-consistency/pbs-metric.png)

Pokud chcete tuto metriku zobrazit, použijte nabídku metrik služby Cosmos DB. V prostředí metrik monitorování Azure se nezobrazí.

## <a name="next-steps"></a>Další kroky

Prostřednictvím následujících dokumentů se můžete dozvědět víc o správě konfliktů dat nebo se přesunout k dalšímu klíčovému konceptu ve službě Cosmos DB:

* [Správa konfliktů mezi oblastmi](how-to-manage-conflicts.md)
* [Dělení a distribuce dat](partition-data.md)
