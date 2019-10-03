---
title: Migrace kontejnerů Azure Cosmos, které nejsou rozdělené na oddíly, do dělených kontejnerů
description: Naučte se migrovat všechny existující nerozdělitelné kontejnery do dělených kontejnerů.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 3a13f8928ba243195c30200dae0525e72c1c161b
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71844406"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrace kontejnerů mimo oddíly na dělené kontejnery

Azure Cosmos DB podporuje vytváření kontejnerů bez klíče oddílu. V současné době můžete vytvářet nerozdělené kontejnery pomocí Azure CLI a Azure Cosmos DB SDK (.NET, Java, NodeJs), jejichž verze je menší nebo rovna 2. x. Pomocí Azure Portal nelze vytvořit kontejnery bez oddílů. Nicméně tyto nerozdělitelné kontejnery nejsou elastické a mají pevnou kapacitu úložiště o velikosti 10 GB a propustnosti 10 000 RU/s.

Kontejnery, které nejsou rozdělené do oddílů, jsou starší a měli byste migrovat existující kontejnery bez oddílů na dělené kontejnery pro škálování úložiště a propustnosti. Azure Cosmos DB poskytuje mechanizmus definovaný systémem pro migraci kontejnerů, které nejsou rozdělené do oddílů, do dělených kontejnerů. Tento dokument vysvětluje, jak se všechny existující kontejnery bez oddílů automaticky migrují do dělených kontejnerů. Funkci automatické migrace můžete využít jenom v případě, že používáte verzi V3 sad SDK ve všech jazycích.

> [!NOTE]
> V současné době nemůžete migrovat Azure Cosmos DB účty rozhraní API MongoDB a Gremlin pomocí kroků popsaných v tomto dokumentu.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrace kontejneru pomocí klíče oddílu definovaného systémem

Pro podporu migrace Azure Cosmos DB poskytne klíč oddílu definovaného systémem s názvem `/_partitionkey` u všech kontejnerů, které nemají klíč oddílu. Po migraci kontejnerů nelze definici klíče oddílu změnit. Například definice kontejneru, který je migrován do děleného kontejneru, bude následující:

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

Po migraci kontejneru můžete vytvořit dokumenty naplněním vlastnosti `_partitionKey` spolu s dalšími vlastnostmi dokumentu. Vlastnost `_partitionKey` představuje klíč oddílu vašich dokumentů.

Výběr správného klíče oddílu je důležitý pro optimální využití zajištěné propustnosti. Další informace najdete v článku [Jak zvolit klíč oddílu](partitioning-overview.md) .

> [!NOTE]
> Můžete využít klíč oddílu definovaného systémem pouze v případě, že ve všech jazycích používáte nejnovější verzi sady SDK verze/v3.

Následující příklad ukazuje vzorový kód pro vytvoření dokumentu s klíčem oddílu definovaného systémem a čtení tohoto dokumentu:

**Reprezentace dokumentu ve formátu JSON**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

Úplnou ukázku najdete v úložišti GitHub [Samples .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) .
                      
## <a name="migrate-the-documents"></a>Migrace dokumentů

I když je definice kontejneru Vylepšená pomocí vlastnosti klíče oddílu, dokumenty v kontejneru se automaticky nemigrují. To znamená, že vlastnost klíče systémového oddílu `/_partitionKey` cesta není automaticky přidána do stávajících dokumentů. Existující dokumenty je potřeba znovu rozdělit na oddíly, které se vytvořily bez klíče oddílu, a znovu je přepsat pomocí vlastnosti `_partitionKey` v dokumentech.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Přístup k dokumentům, které nemají klíč oddílu

Aplikace mají přístup k existujícím dokumentům, které nemají klíč oddílu, pomocí speciální systémové vlastnosti s názvem "PartitionKey. None", jedná se o hodnotu nemigrovaných dokumentů. Tuto vlastnost můžete použít ve všech operacích CRUD a dotazování. Následující příklad ukazuje ukázku pro čtení jednoho dokumentu z NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Kompletní ukázku, jak změnit oddíly dokumentů, najdete v úložišti GitHub [Samples .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) . 

## <a name="compatibility-with-sdks"></a>Kompatibilita se sadami SDK

Starší verze sady Azure Cosmos DB SDK, například v2. x. x a v1. x. x, nepodporují vlastnost klíče oddílu definované systémem. Takže při čtení definice kontejneru ze starší sady SDK neobsahuje žádné definice klíče oddílu a tyto kontejnery se budou chovat přesně stejně jako předtím. Aplikace, které jsou vytvořeny pomocí starší verze sad SDK, budou nadále fungovat s nerozdělenými na oddíly beze změn. 

Pokud je migrovaný kontejner využíván nejnovější verzí sady SDK a vy začnete naplnit klíč oddílu definovaného systémem v nových dokumentech, nebudete již mít přístup k takovým dokumentům ze starších sad SDK (čtení, aktualizace, odstranění a dotazování).

## <a name="next-steps"></a>Další kroky

* [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
* [Jednotky žádostí ve službě Azure Cosmos DB](request-units.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Práce s účtem Azure Cosmos](account-overview.md)