---
title: Naučte se konfigurovat a spravovat dobu v provozu v Azure Cosmos DB
description: Naučte se konfigurovat a spravovat dobu v provozu v Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: mjbrown
ms.openlocfilehash: ddda7b96147892efb38cb0405120db3613e98cf8
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104872"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Konfigurace času na živé v Azure Cosmos DB

V Azure Cosmos DB můžete nastavit hodnotu TTL (Time to Live) na úrovni kontejneru nebo ji můžete přepsat na úrovni položky po nastavení kontejneru. Hodnotu TTL pro kontejner můžete nakonfigurovat pomocí Azure Portal nebo sad SDK pro konkrétní jazyk. Přepsání na úrovni položek TTL lze konfigurovat pomocí sad SDK.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Povolit dobu provozu na kontejneru pomocí Azure Portal

Pomocí následujících kroků můžete povolit dobu provozu na kontejneru bez vypršení platnosti. Tuto možnost povolte, pokud chcete, aby hodnota TTL mohla být přepsána na úrovni položky. Hodnotu TTL můžete nastavit také zadáním nenulové hodnoty pro sekundy.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vytvořte nový účet Azure Cosmos nebo vyberte existující účet.

3. Otevřete podokno **Průzkumník dat** .

4. Vyberte existující kontejner, rozbalte jej a upravte následující hodnoty:

   * Otevřete okno **nastavení & škálování** .
   * V části **Nastavení** najít, **Doba do provozu**.
   * Vyberte **zapnuto (žádné výchozí)** nebo **Vyberte a nastavte** hodnotu TTL.
   * Kliknutím na **Uložit** uložte změny.

   ![Konfigurace času na živé v Azure Portal](./media/how-to-time-to-live/how-to-time-to-live-portal.png)


- Pokud má DefaultTimeToLive hodnotu null, váš čas do živého provozu je vypnutý.
- Pokud je DefaultTimeToLive-1, je nastavení Time to Live zapnuté (žádné výchozí).
- Pokud má DefaultTimeToLive jinou hodnotu typu int (s výjimkou 0), je zapnutá možnost čas do živého nastavení.

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Povolení doby provozu na kontejneru pomocí sady SDK

### <a id="dotnet-enable-noexpiry"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

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

### <a id="dotnet-enable-noexpiry"></a>.NET SDK V3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Nastavení TTL (Time to Live) na kontejneru pomocí sady SDK

Chcete-li nastavit hodnotu TTL (Time to Live) na kontejneru, je nutné zadat nenulové kladné číslo, které určuje časové období v sekundách. Na základě nastavené hodnoty TTL se odstraní všechny položky v kontejneru po posledním změněném časovém razítku položky `_ts` .

### <a id="dotnet-enable-withexpiry"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

### <a id="dotnet-enable-withexpiry"></a>.NET SDK V3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
});
```

### <a id="nodejs-enable-withexpiry"></a>Sada NodeJS SDK

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

* Chcete-li pro položku nastavit hodnotu TTL, je nutné zadat nenulové kladné číslo, které určuje dobu, po kterou má být položka po posledním změněném časovém razítku položky `_ts`vypršet.

* Pokud položka nemá pole TTL, bude ve výchozím nastavení pro položku platit hodnota TTL nastavená na kontejner.

* Pokud je hodnota TTL na úrovni kontejneru zakázaná, bude pole TTL u této položky ignorováno, dokud nebude hodnota TTL znovu povolena v kontejneru.

### <a id="portal-set-ttl-item"></a>Azure Portal

Chcete-li povolit dobu provozu na položce, použijte následující postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vytvořte nový účet Azure Cosmos nebo vyberte existující účet.

3. Otevřete podokno **Průzkumník dat** .

4. Vyberte existující kontejner, rozbalte jej a upravte následující hodnoty:

   * Otevřete okno **nastavení & škálování** .
   * V části **Nastavení** najít, **Doba do provozu**.
   * Vyberte **zapnuto (žádné výchozí)** nebo **Vyberte a nastavte** hodnotu TTL. 
   * Kliknutím na **Uložit** uložte změny.

5. V dalším kroku přejděte na položku, pro kterou chcete nastavit hodnotu TTL (Time to Live `ttl` ), přidejte vlastnost a vyberte **aktualizovat**. 

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

### <a id="dotnet-set-ttl-item"></a>.NET SDK (jakékoli)

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

### <a id="nodejs-set-ttl-item"></a>Sada NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value", 
          ttl: 2
        };
```


## <a name="reset-time-to-live"></a>Resetovat čas na Live

Čas do živého nastavení můžete nastavit tak, že na položku provedete operaci zápisu nebo aktualizace. Operace zápisu nebo aktualizace nastaví `_ts` na aktuální čas a hodnota TTL pro položku, jejíž platnost vyprší, bude zahájena znovu. Pokud chcete změnit hodnotu TTL položky, můžete pole aktualizovat stejně, jako byste aktualizovali jiné pole.

### <a id="dotnet-extend-ttl-item"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

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

### <a id="dotnet-extend-ttl-item"></a>.NET SDK V3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

## <a name="turn-off-time-to-live"></a>Vypnutí času na živé

Pokud je u položky nastavená možnost čas na Live a už nechcete, aby tato položka vypršela, pak můžete získat položku, odebrat pole TTL a nahradit položku na serveru. Po odebrání pole TTL z položky se na položku použije výchozí hodnota TTL přiřazená kontejneru. Nastavte hodnotu TTL na hodnotu-1, pokud chcete zabránit vypršení platnosti položky a Nedědit hodnotu TTL z kontejneru.

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

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

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK V3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

## <a name="disable-time-to-live"></a>Zakázat dobu do provozu

Chcete-li zakázat dobu provozu na kontejneru a zastavit proces na pozadí, aby kontroloval položky, jejichž platnost `DefaultTimeToLive` vypršela, měla by být vlastnost kontejneru odstraněna. Odstranění této vlastnosti se liší od nastavení na hodnotu-1. Když nastavíte hodnotu-1, nové položky přidané do kontejneru budou trvale živé, ale tuto hodnotu můžete přepsat pro konkrétní položky v kontejneru. Po odebrání vlastnosti TTL z kontejneru nebudou položky nikdy vypršet, a to ani v případě, že jste explicitně přepsali předchozí výchozí hodnotu TTL.

### <a id="dotnet-disable-ttl"></a>.NET SDK v2 (Microsoft. Azure. DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

### <a id="dotnet-disable-ttl"></a>.NET SDK V3 (Microsoft. Azure. Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="next-steps"></a>Další postup

Další informace o službě Time to Live v následujícím článku:

* [Doba do provozu](time-to-live.md)
