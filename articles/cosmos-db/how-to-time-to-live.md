---
title: Konfigurace a Správa času do provozu v Azure Cosmos DB
description: Naučte se, jak nakonfigurovat a spravovat dobu provozu na kontejneru a položku v Azure Cosmos DB
author: anfeldma-ms
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/27/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 5a310dca40b8f5fea074c0cd3c75751d62ccb8f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91297891"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Konfigurace času na živé v Azure Cosmos DB

V Azure Cosmos DB můžete nastavit hodnotu TTL (Time to Live) na úrovni kontejneru nebo ji můžete přepsat na úrovni položky po nastavení kontejneru. Hodnotu TTL pro kontejner můžete nakonfigurovat pomocí Azure Portal nebo sad SDK pro konkrétní jazyk. Přepsání na úrovni položek TTL lze konfigurovat pomocí sad SDK.

> Tento obsah se týká Azure Cosmos DB hodnoty TTL transakčního úložiště. Pokud hledáte hodnotu TTL pro analitycal Store, která umožňuje scénáře NoETL HTAP prostřednictvím [odkazu Azure synapse](https://docs.microsoft.com/azure/cosmos-db/synapse-link), klikněte prosím [sem](https://docs.microsoft.com/azure/cosmos-db/analytical-store-introduction#analytical-ttl).

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Povolit dobu provozu na kontejneru pomocí Azure Portal

Pomocí následujících kroků můžete povolit dobu provozu na kontejneru bez vypršení platnosti. Tuto možnost povolte, pokud chcete, aby hodnota TTL mohla být přepsána na úrovni položky. Hodnotu TTL můžete nastavit také zadáním nenulové hodnoty pro sekundy.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

2. Vytvořte nový účet Azure Cosmos nebo vyberte existující účet.

3. Otevřete podokno **Průzkumník dat** .

4. Vyberte existující kontejner, rozbalte jej a upravte následující hodnoty:

   * Otevřete okno **nastavení & škálování** .
   * V části **Nastavení** najít, **Doba do provozu**.
   * Vyberte **zapnuto (žádné výchozí)** nebo **Vyberte a nastavte** hodnotu TTL.
   * Kliknutím na **Uložit** uložte změny.

   :::image type="content" source="./media/how-to-time-to-live/how-to-time-to-live-portal.png" alt-text="Konfigurace času na živé v Azure Portal":::

* Pokud má DefaultTimeToLive hodnotu null, váš čas do živého provozu je vypnutý.
* Pokud je DefaultTimeToLive-1, je nastavení Time to Live zapnuté (žádné výchozí).
* Pokud má DefaultTimeToLive jinou hodnotu typu int (s výjimkou 0), je zapnutá možnost čas do živého nastavení.

## <a name="enable-time-to-live-on-a-container-using-azure-cli-or-powershell"></a>Povolení doby provozu na kontejneru pomocí Azure CLI nebo PowerShellu

Pokud chcete vytvořit nebo povolit hodnotu TTL u kontejneru, přečtěte si téma.

* [Vytvoření kontejneru s hodnotou TTL pomocí Azure CLI](manage-with-cli.md#create-a-container-with-ttl)
* [Vytvoření kontejneru s hodnotou TTL pomocí PowerShellu](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Povolení doby provozu na kontejneru pomocí sady SDK

### <a name="net-sdk"></a><a id="dotnet-enable-noexpiry"></a> SADA .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK v2 (Microsoft.Azure.DocumentDB)

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```
---

### <a name="java-sdk"></a><a id="java-enable-noexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK v4](#tab/javav4)

Java SDK v4 (Maven com. Azure:: Azure-Cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven com. Microsoft. Azure:: Azure-Cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Nastavení TTL (Time to Live) na kontejneru pomocí sady SDK

Chcete-li nastavit hodnotu TTL (Time to Live) na kontejneru, je nutné zadat nenulové kladné číslo, které určuje časové období v sekundách. Na základě nastavené hodnoty TTL se odstraní všechny položky v kontejneru po posledním změněném časovém razítku položky `_ts` .

### <a name="net-sdk"></a><a id="dotnet-enable-withexpiry"></a> SADA .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK v2 (Microsoft.Azure.DocumentDB)

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days
});
```
---

### <a name="java-sdk"></a><a id="java-enable-defaultexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK v4](#tab/javav4)

Java SDK v4 (Maven com. Azure:: Azure-Cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven com. Microsoft. Azure:: Azure-Cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="nodejs-sdk"></a><a id="nodejs-enable-withexpiry"></a>Sada NodeJS SDK

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

## <a name="set-time-to-live-on-an-item"></a>Nastavení času na živé pro položku

Kromě nastavení výchozí hodnoty TTL (Time to Live) na kontejneru můžete nastavit hodnotu TTL (Time to Live) pro položku. Nastavením hodnoty doba na živý na úrovni položky se přepíše výchozí hodnota TTL položky v tomto kontejneru.

* Chcete-li pro položku nastavit hodnotu TTL, je nutné zadat nenulové kladné číslo, které určuje dobu, po kterou má být položka po posledním změněném časovém razítku položky vypršet `_ts` .

* Pokud položka nemá pole TTL, bude ve výchozím nastavení pro položku platit hodnota TTL nastavená na kontejner.

* Pokud je hodnota TTL na úrovni kontejneru zakázaná, bude pole TTL u této položky ignorováno, dokud nebude hodnota TTL znovu povolena v kontejneru.

### <a name="azure-portal"></a><a id="portal-set-ttl-item"></a>Azure Portal

Chcete-li povolit dobu provozu na položce, použijte následující postup:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

2. Vytvořte nový účet Azure Cosmos nebo vyberte existující účet.

3. Otevřete podokno **Průzkumník dat** .

4. Vyberte existující kontejner, rozbalte jej a upravte následující hodnoty:

   * Otevřete okno **nastavení & škálování** .
   * V části **Nastavení** najít, **Doba do provozu**.
   * Vyberte **zapnuto (žádné výchozí)** nebo **Vyberte a nastavte** hodnotu TTL. 
   * Kliknutím na **Uložit** uložte změny.

5. V dalším kroku přejděte na položku, pro kterou chcete nastavit hodnotu TTL (Time to Live), přidejte `ttl` vlastnost a vyberte **aktualizovat**. 

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

### <a name="net-sdk-any"></a><a id="dotnet-set-ttl-item"></a>.NET SDK (jakékoli)

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

### <a name="nodejs-sdk"></a><a id="nodejs-set-ttl-item"></a>Sada NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value",
          ttl: 2
        };
```

### <a name="java-sdk"></a><a id="java-set-ttl-item"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK v4](#tab/javav4)

Java SDK v4 (Maven com. Azure:: Azure-Cosmos)

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

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven com. Microsoft. Azure:: Azure-Cosmos)

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
---

## <a name="reset-time-to-live"></a>Resetovat čas na Live

Čas do živého nastavení můžete nastavit tak, že na položku provedete operaci zápisu nebo aktualizace. Operace zápisu nebo aktualizace nastaví na `_ts` aktuální čas a hodnota TTL pro položku, jejíž platnost vyprší, bude zahájena znovu. Pokud chcete změnit hodnotu TTL položky, můžete pole aktualizovat stejně, jako byste aktualizovali jiné pole.

### <a name="net-sdk"></a><a id="dotnet-extend-ttl-item"></a> SADA .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK v2 (Microsoft.Azure.DocumentDB)

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```
---

### <a name="java-sdk"></a><a id="java-enable-modifyitemexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK v4](#tab/javav4)

Java SDK v4 (Maven com. Azure:: Azure-Cosmos)

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

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Sada SDK V3 (Maven com. Microsoft. Azure:: Azure-Cosmos)

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
---

## <a name="turn-off-time-to-live"></a>Vypnutí času na živé

Pokud je u položky nastavená možnost čas na Live a už nechcete, aby tato položka vypršela, pak můžete získat položku, odebrat pole TTL a nahradit položku na serveru. Po odebrání pole TTL z položky se na položku použije výchozí hodnota TTL přiřazená kontejneru. Nastavte hodnotu TTL na hodnotu-1, pokud chcete zabránit vypršení platnosti položky a Nedědit hodnotu TTL z kontejneru.

### <a name="net-sdk"></a><a id="dotnet-turn-off-ttl-item"></a> SADA .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK v2 (Microsoft.Azure.DocumentDB)

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```
---

### <a name="java-sdk"></a><a id="java-enable-itemdefaultexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK v4](#tab/javav4)

Java SDK v4 (Maven com. Azure:: Azure-Cosmos)

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

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven com. Microsoft. Azure:: Azure-Cosmos)

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
---

## <a name="disable-time-to-live"></a>Zakázat dobu do provozu

Chcete-li zakázat dobu provozu na kontejneru a zastavit proces na pozadí, aby kontroloval položky, jejichž platnost vypršela, `DefaultTimeToLive` měla by být vlastnost kontejneru odstraněna. Odstranění této vlastnosti se liší od nastavení na hodnotu-1. Když nastavíte hodnotu-1, nové položky přidané do kontejneru budou trvale živé, ale tuto hodnotu můžete přepsat pro konkrétní položky v kontejneru. Po odebrání vlastnosti TTL z kontejneru nebudou položky nikdy vypršet, a to ani v případě, že jste explicitně přepsali předchozí výchozí hodnotu TTL.

### <a name="net-sdk"></a><a id="dotnet-disable-ttl"></a> SADA .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK v2 (Microsoft.Azure.DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft. Azure. Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```
---

### <a name="java-sdk"></a><a id="java-enable-disableexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK v4](#tab/javav4)

Java SDK v4 (Maven com. Azure:: Azure-Cosmos)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.setDefaultTimeToLiveInSeconds(null);
// Update container settings
container.replace(containerProperties).block();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven com. Microsoft. Azure:: Azure-Cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.defaultTimeToLive(null);
// Update container settings
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

## <a name="next-steps"></a>Další kroky

Další informace o službě Time to Live v následujícím článku:

* [Hodnota TTL (Time to Live)](time-to-live.md)
