---
title: Konfigurace a správa času pro život v Azure Cosmos DB
description: Zjistěte, jak nakonfigurovat a spravovat čas pro život v kontejneru a položce v Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: anfeldma
ms.openlocfilehash: 72653a3b28181316a2bf7dd7e73f2685c3afcf73
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384258"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Konfigurace času pro život v Azure Cosmos DB

V Azure Cosmos DB můžete nakonfigurovat Time to Live (TTL) na úrovni kontejneru, nebo můžete přepsat na úrovni položky po nastavení pro kontejner. TTL pro kontejner můžete nakonfigurovat pomocí portálu Azure nebo sad SDK specifických pro daný jazyk. Přepsání TTL na úrovni položky lze konfigurovat pomocí sad SDK.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Povolení času na kontejneru pomocí portálu Azure Portal

Pomocí následujících kroků povolte čas žít na kontejneru bez vypršení platnosti. Povolte tuto možnost, chcete-li povolit TTL přepsat na úrovni položky. TTL můžete také nastavit zadáním nenulové hodnoty pro sekundy.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Vytvořte si nový účet Azure Cosmos nebo vyberte existující účet.

3. Otevřete podokno **Průzkumník dat.**

4. Vyberte existující kontejner, rozbalte ho a upravte následující hodnoty:

   * Otevřete okno **Změnit & nastavení.**
   * V části **Nastavení** najít, **Čas žít**.
   * Vyberte **Zapnuto (bez výchozího nastavení)** nebo vyberte **Zapnuto** a nastavte hodnotu TTL.
   * Kliknutím na **Uložit** uložte změny.

   ![Konfigurace času pro život na webu Azure Portal](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

* Když DefaultTimeToLive je null pak váš čas žít je pryč
* Když DefaultTimeToLive je -1, pak je vaše nastavení Time to Live zapnuto (bez výchozího nastavení)
* Pokud má DefaultTimeToLive jinou hodnotu Int (kromě 0), je vaše nastavení Time to Live zapnuto

## <a name="enable-time-to-live-on-a-container-using-azure-cli-or-powershell"></a>Povolení času na kontejneru pomocí azure cli nebo PowerShellu

Chcete-li vytvořit nebo povolit TTL v kontejneru, viz,

* [Vytvoření kontejneru s ttl pomocí Azure CLI](manage-with-cli.md#create-a-container-with-ttl)
* [Vytvoření kontejneru s TTL pomocí PowerShellu](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Povolení doby života v kontejneru pomocí sady SDK

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-enable-noexpiry"></a>Sada .NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition);
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-enable-noexpiry"></a>Sada .NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-noexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-noexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Nastavení času pro život v kontejneru pomocí sady SDK

Chcete-li nastavit čas žít v kontejneru, je třeba zadat nenulové kladné číslo, které označuje časové období v sekundách. Na základě nakonfigurované hodnoty TTL jsou odstraněny všechny položky v kontejneru po posledním změněném časovém razítku položky. `_ts`

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-enable-withexpiry"></a>Sada .NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-enable-withexpiry"></a>Sada .NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
});
```

### <a name="nodejs-sdk"></a><a id="nodejs-enable-withexpiry"></a>NodeJS SDK

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-defaultexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-defaultexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

## <a name="set-time-to-live-on-an-item"></a>Nastavení času na život s položkou

Kromě nastavení výchozího času pro život v kontejneru můžete nastavit čas, který se má žít pro položku. Nastavení času na úroveň položky přepíše výchozí TTL položky v tomto kontejneru.

* Chcete-li nastavit TTL na položku, je třeba zadat nenulové kladné číslo, které označuje období v sekundách, `_ts`kdy vyprší platnost položky po posledním upraveném časovém razítku položky .

* Pokud položka nemá pole TTL, pak ve výchozím nastavení ttl nastavené na kontejner se bude vztahovat na položku.

* Pokud je ttl zakázána na úrovni kontejneru, pole TTL na položce bude ignorováno, dokud nebude ttl znovu povoleno v kontejneru.

### <a name="azure-portal"></a><a id="portal-set-ttl-item"></a>Portál Azure

Chcete-li povolit, aby čas života na položce mohl být:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Vytvořte si nový účet Azure Cosmos nebo vyberte existující účet.

3. Otevřete podokno **Průzkumník dat.**

4. Vyberte existující kontejner, rozbalte ho a upravte následující hodnoty:

   * Otevřete okno **Změnit & nastavení.**
   * V části **Nastavení** najít, **Čas žít**.
   * Vyberte **Zapnuto (bez výchozího nastavení)** nebo vyberte **Zapnuto** a nastavte hodnotu TTL. 
   * Kliknutím na **Uložit** uložte změny.

5. Dále přejděte k položce, pro kterou chcete `ttl` nastavit čas na život, přidejte vlastnost a vyberte **Aktualizovat**. 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a name="net-sdk-any"></a><a id="dotnet-set-ttl-item"></a>Sada .NET SDK (libovolná)

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a name="nodejs-sdk"></a><a id="nodejs-set-ttl-item"></a>NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value",
          ttl: 2
        };
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-itemexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-itemexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public void id(String new_id) {this.id = new_id;}
    public String customerId() {return this.customerId;}
    public void customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public void ttl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```

## <a name="reset-time-to-live"></a>Obnovit dobu života

Čas, který chcete u položky obnovit, můžete obnovit provedením operace zápisu nebo aktualizace položky. Operace zápisu `_ts` nebo aktualizace nastaví aktuální čas a TTL pro položku vyprší znovu. Chcete-li změnit ttl položky, můžete pole aktualizovat stejně jako jakékoli jiné pole.

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-extend-ttl-item"></a>Sada .NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-extend-ttl-item"></a>Sada .NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-modifyitemexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-modifyitemexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```

## <a name="turn-off-time-to-live"></a>Vypnout čas žít

Pokud byl u položky nastaven čas na život a vy již nechcete, aby tato položka vypršela, můžete položku získat, odebrat pole TTL a nahradit položku na serveru. Při odebrání pole TTL z položky se na položku použije výchozí hodnota TTL přiřazená kontejneru. Nastavte hodnotu TTL na -1, abyste zabránili vypršení platnosti položky a nezdědili hodnotu TTL z kontejneru.

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-turn-off-ttl-item"></a>Sada .NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the container

response = await client.ReplaceDocumentAsync(readDocument);
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-turn-off-ttl-item"></a>Sada .NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-itemdefaultexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(null);
            return container.createItem(salesOrder);
}).block();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-itemdefaultexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(null);
            return container.createItem(salesOrder);
}).block();
```

## <a name="disable-time-to-live"></a>Zakázat čas na život

Chcete-li zakázat čas žít na kontejneru a zastavit proces `DefaultTimeToLive` na pozadí z kontroly položek vypršela, vlastnost v kontejneru by měly být odstraněny. Odstranění této vlastnosti se liší od nastavení na -1. Když ji nastavíte na -1, nové položky přidané do kontejneru budou navždy žít, ale tuto hodnotu můžete přepsat u konkrétních položek v kontejneru. Při odebrání Vlastnost TTL z kontejneru položky nikdy nevyprší, i v případě, že mají explicitně přepsána předchozí výchozí hodnotu TTL.

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-disable-ttl"></a>Sada .NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-disable-ttl"></a>Sada .NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-disableexpiry"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.setDefaultTimeToLiveInSeconds(null);
// Update container settings
container.replace(containerProperties).block();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-disableexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.defaultTimeToLive(null);
// Update container settings
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

## <a name="next-steps"></a>Další kroky

Další informace o čase žít v následujícím článku:

* [Čas žít](time-to-live.md)
