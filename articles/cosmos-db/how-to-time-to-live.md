---
title: Zjistěte, jak konfigurovat a spravovat TTL ve službě Azure Cosmos DB
description: Zjistěte, jak konfigurovat a spravovat TTL ve službě Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/14/2018
ms.author: mjbrown
ms.openlocfilehash: 209234e91d0ff788bf828dae0e56f37f9921b5c1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835016"
---
# <a name="how-to-configure-time-to-live-in-azure-cosmos-db"></a>Jak nakonfigurovat TTL ve službě Azure Cosmos DB

Ve službě Azure Cosmos DB můžete nakonfigurovat čas to Live (TTL) na úrovni kontejneru, nebo je možné ho přepsat na úrovni položky po nastavení pro kontejner. Hodnota TTL můžete nakonfigurovat pro kontejner pomocí webu Azure portal nebo pomocí sady SDK pro konkrétní jazyk. Položka úrovně TTL přepsání lze nastavit pomocí sad SDK.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Povolit TTL kontejneru pomocí webu Azure portal

Následujícím postupem povolte TTL kontejneru bez časového omezení. Povolte povolíte TTL možné přepsat na úrovni položek. Hodnota TTL můžete také nastavit tak, že zadáte jiné hodnoty než nula sekund.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vytvořit nový účet Azure Cosmos, nebo vyberte existující účet.

3. Otevřít **Průzkumník dat** podokně.

4. Vybrat existující kontejner, rozbalte ho a upravte následující hodnoty:

   * Otevřít **škálování a nastavení** okna.
   * V části **nastavení** najdete **TTL**.
   * Vyberte **na (žádná výchozí hodnota)** nebo vyberte **na** a nastavit hodnotu TTL
   * Kliknutím na **Uložit** uložte změny.

   ![Nakonfigurujte čas TTL na webu Azure portal](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Povolit TTL kontejneru pomocí sady SDK

### <a id="dotnet-enable-noexpiry"></a>.NET SDK

```csharp
// Create a new collection with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Nastavit čas TTL kontejneru pomocí sady SDK

### <a id="dotnet-enable-withexpiry"></a>.NET SDK

K nastavení time to live v kontejneru, budete muset poskytnout nenulové kladné číslo určující doba v sekundách. Použije se konfigurovaná hodnota TTL podle, všechny položky v kontejneru po poslední úpravy časového razítka položky `_ts` se odstraní.

```csharp
// Create a new collection with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

## <a name="set-time-to-live-on-an-item"></a>Nastavit čas TTL na položku

Kromě nastavení výchozí čas TTL na kontejner, můžete nastavit čas TTL pro položku. Nastavení času na za provozu na úrovni položek se přepíšou výchozí hodnota TTL položky v tomto kontejneru.

* Pokud chcete nastavit interval TTL, ZÍSKÁ na položku, budete muset zadat nenulové kladné číslo, který určuje dobu v sekundách, po vypršení platnosti položky po poslední úpravy časového razítka položky `_ts`.

* Pokud položka neobsahuje hodnotu TTL pole, pak ve výchozím nastavení, interval TTL, ZÍSKÁ nastavena na kontejner použije k položce.

* Pokud hodnota TTL je zakázaná na úrovni kontejneru, pole hodnoty TTL u položky budou ignorovány, dokud hodnota TTL je znovu povolit v kontejneru.

### <a id="dotnet-set-ttl-item"></a>.NET SDK

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
    public int? TimeToLive { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

## <a name="reset-time-to-live"></a>Resetovat čas TTL

Můžete resetovat time to live pomocí provádí zápis na položku nebo aktualizovat operace na položce. Operace zápisu nebo aktualizace nastaví `_ts` na aktuální čas a interval TTL, ZÍSKÁ položky do vypršení platnosti začne znovu. Pokud chcete změnit interval TTL, ZÍSKÁ položky, můžete aktualizovat pole, stejně jako aktualizace jakékoli jiné pole.

### <a id="dotnet-extend-ttl-item"></a>.NET SDK

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.TimeToLive = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="turn-off-time-to-live"></a>Vypnout TTL

Pokud už nechcete tuto položku do vypršení platnosti TTL je nastavená na položku, pak můžete získat položku, odeberte pole TTL a nahradit položku na serveru. Pokud pole Hodnota TTL je odebrán z položky, výchozí hodnota TTL přiřazené ke kontejneru je použitý pro položku. Hodnota TTL nastavena na hodnotu -1, jak zabránit vypršení platnosti položky a hodnota TTL není odvozena od kontejneru.

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.TimeToLive = null; // inherit the default TTL of the collection

response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="disable-time-to-live"></a>Zakázat TTL

Zakázat čas za provozu na kontejner a zastavte sledovací proces na pozadí od kontroly pro vypršela platnost položky `DefaultTimeToLive` vlastnost v kontejneru, měla by být odstraněna. Odstraňuje se tato vlastnost se liší od nastavení na hodnotu -1. Pokud ji nastavíte na hodnotu -1, nové položky přidané do tohoto kontejneru se navždy, živé však můžete přepsat tuto hodnotu na konkrétní položky v kontejneru. Při odebrání vlastnosti TTL z kontejneru položky vyprší platnost, ani když se, že je mít explicitně přepsat předchozí výchozí hodnota TTL.

### <a id="dotnet-disable-ttl"></a>.NET SDK

```csharp
// Get the collection, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

## <a name="next-steps"></a>Další postup

Další informace o TTL v následujícím článku:

* [Hodnota Time to live](time-to-live.md)
