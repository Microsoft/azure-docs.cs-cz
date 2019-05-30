---
title: Migrace bez oddílů kontejnery služby Azure Cosmos DB do dělené kontejnery
description: Zjistěte, jak migrovat všechny existující kontejnery bez oddílů do dělené kontejnerů.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 8ba9489496a8f9e3703702e344684b4028a002cc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241928"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrace bez oddílů kontejnery do dělené kontejnery

Azure Cosmos DB podporuje vytváření kontejnerů bez klíče oddílu. Nyní můžete vytvořit bez oddílů kontejnery pomocí rozhraní příkazového řádku Azure a Azure Cosmos DB SDK (.Net, Java, NodeJs), které mají verzi nižší než nebo rovna 2.x. Nelze vytvořit bez oddílů kontejnerů pomocí webu Azure portal. Takových kontejnerů bez oddílů však nejsou elastický a opravili úložnou kapacitu limit 10 GB a propustnosti 10 tisíc RU/s.

Kontejnery bez oddílů jsou starší verze a měli byste migrovat existující kontejnery bez oddílů na dělené kontejnerů a škálovatelného úložiště a propustnost. Azure Cosmos DB poskytuje mechanismus definovaná systémem k migraci kontejnerů bez oddílů do dělené kontejnerů. Tento dokument popisuje, jak automaticky migrovat do dělené kontejnerů jsou všechny existující kontejnery bez oddílů. Můžete využít funkci automatické migraci pouze v případě, že používáte V3 verzi sady SDK ve všech jazycích.

> [!NOTE] 
> V současné době nemůžete migrovat účty Azure Cosmos DB MongoDB, Gremlin API pomocí kroků popsaných v tomto dokumentu. 

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrace kontejner pomocí klíče oddílu definovaná systémem

Pro podporu migrace, Azure Cosmos DB definuje definovaná systémem klíče oddílu s názvem `/_partitionkey` na všechny kontejnery, které nemají klíč oddílu. Definice klíče oddílu nelze změnit po migraci kontejnerů. Definice kontejneru, který je přenášen do dělené kontejneru bude třeba takto: 

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
 
Po migraci oznámení o kontejneru, můžete vytvořit dokumenty naplněním `_partitionKey` vlastnost spolu s ostatními vlastnostmi dokumentu. `_partitionKey` Vlastnost představuje klíč oddílu dokumentů. 

Zvolit klíč oddílu vpravo je důležité pro optimální využití zřízené propustnosti. Další informace najdete v tématu [jak zvolit klíč oddílu](partitioning-overview.md) článku. 

> [!NOTE]
> Pouze v případě, že používáte nejnovější verzi nebo V3 verzi sady SDK ve všech jazycích, můžete využít výhod systému definovaným klíčem oddílu.

Následující příklad ukazuje ukázkový kód pro vytvoření dokumentu s klíčem oddílu definovaný systému a přečtěte si tento dokument:

**Reprezentace JSON dokumentu**

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

    [JsonProperty(PropertyName = "_partitionKey")]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234", 
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

CosmosItemResponse<DeviceInformationItem > response = 
  await migratedContainer.Items.CreateItemAsync(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
CosmosItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  ); 

```

Úplnou ukázku najdete v tématu [ukázky .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) úložiště GitHub. 
                      
## <a name="migrate-the-documents"></a>Migrace dokumenty

Při definici kontejneru je rozšířené vlastnosti klíče oddílu, dokumenty v kontejneru se automaticky migrovat. To znamená, že vlastnost klíče oddílu systému `/_partitionKey` cesta není automaticky přidán do existující dokumenty. Budete muset změnit rozdělení stávající dokumenty načtením dokumentů, které byly vytvořeny bez klíče oddílu a jejich zpět s přepsáním `_partitionKey` vlastností v dokumentech. 

## <a name="access-documents-that-dont-have-a-partition-key"></a>Získáte přístup k dokumentům, které nemají klíče oddílu

Aplikace můžou k existující dokumenty, které nemají klíče oddílu pomocí speciální systémové vlastnosti s názvem "CosmosContainerSettings.NonePartitionKeyValue", jedná se o hodnotu nemigrované dokumentů. Tuto vlastnost můžete použít u všech operací CRUD a dotazů. Následující příklad ukazuje ukázkové čtení jednoho dokumentu z NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: CosmosContainerSettings.NonePartitionKeyValue, 
  id: device.Id
); 

```

Kompletní příklad, jak změnit rozdělení dokumenty, najdete v článku [ukázky .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) úložiště GitHub. 

## <a name="compatibility-with-sdks"></a>Kompatibilita s SDK

Starší verzi sady SDK služby Azure Cosmos DB například V2.x.x a V1.x.x nepodporují vlastnost klíče oddílu definovaná systémem. Proto při čtení definici kontejneru ze starší sada SDK neobsahuje žádné definice klíče oddílu a tyto kontejnery se bude chovat úplně stejně jako předtím. Aplikace, které jsou vytvořeny pomocí starší verze sady SDK i nadále pracovat bez oddílů je bez nutnosti jakkoli měnit. 

Pokud migrované kontejneru je využívána nejnovější/V3 verzi sady SDK a spustit sestavování definovaná systémem klíče oddílu v rámci nové dokumenty, nemáte přístup (čtení, aktualizace, odstranění, dotaz), zobrazovat dokumenty ze starších sad SDK.

## <a name="next-steps"></a>Další postup

* [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
* [Jednotky žádosti v Azure Cosmos DB](request-units.md)
* [Zřizování propustnosti na kontejnerech a databází](set-throughput.md)
* [Práce s účtem Azure Cosmos](account-overview.md)