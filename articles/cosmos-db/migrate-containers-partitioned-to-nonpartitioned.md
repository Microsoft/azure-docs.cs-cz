---
title: Migrace nerozdělených kontejnerů Azure Cosmos do dělených kontejnerů
description: Zjistěte, jak migrovat všechny existující nerozdělené kontejnery do dělených kontejnerů.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 742ef62895f3ef64e8fa22ab21d2947bee57776b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623357"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrace nerozdělených kontejnerů do dělených kontejnerů

Azure Cosmos DB podporuje vytváření kontejnerů bez klíče oddílu. V současné době můžete vytvořit kontejnery bez oddílů pomocí Azure CLI a Azure Cosmos DB SDKs (.Net, Java, NodeJs), které mají verzi menší nebo rovnou 2.x. Nelze vytvořit kontejnery bez oddílů pomocí portálu Azure. Takové nerozdělené kontejnery však nejsou elastické a mají pevnou úložnou kapacitu 20 GB a limit propustnosti 10 kS RU/s.

Kontejnery bez oddílů jsou starší verze a měli byste migrovat existující nerozdělené kontejnery do dělených kontejnerů pro škálování úložiště a propustnost. Azure Cosmos DB poskytuje systémově definovaný mechanismus pro migraci nerozdělených kontejnerů do dělených kontejnerů. Tento dokument vysvětluje, jak jsou všechny existující nerozdělené kontejnery automaticky přeneseny do dělených kontejnerů. Funkci automatické migrace můžete využít pouze v případě, že používáte verzi sady SDK v jazycích V3 ve všech jazycích.

> [!NOTE]
> V současné době nelze migrovat účty Azure Cosmos DB MongoDB a Gremlin API pomocí kroků popsaných v tomto dokumentu.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrace kontejneru pomocí systémově definovaného klíče oddílu

Pro podporu migrace, Azure Cosmos DB poskytuje `/_partitionkey` systémem definovaný klíč oddílu s názvem na všechny kontejnery, které nemají klíč oddílu. Po migraci kontejnerů nelze změnit definici klíče oddílu. Například definice kontejneru, který je migrován do děleného kontejneru bude následující:

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

Po migraci kontejneru můžete vytvořit dokumenty vyplněním `_partitionKey` vlastnosti spolu s dalšími vlastnostmi dokumentu. Vlastnost `_partitionKey` představuje klíč oddílu dokumentů.

Výběr správného klíče oddílu je důležité optimálně využít zřízenou propustnost. Další informace naleznete v tématu jak vybrat článek [o klíči oddílu.](partitioning-overview.md)

> [!NOTE]
> Klíč oddílu definovaný systémem můžete využít pouze v případě, že používáte nejnovější verzi sad SDK v jazycích SDK.You can advantage of system defined partition key only if you are using the latest/V3 version of SDKs in all the languages.

Následující příklad ukazuje ukázkový kód pro vytvoření dokumentu se systémem definovaným klíčem oddílu a čtení tohoto dokumentu:

**JSON reprezentace dokumentu**

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

Kompletní ukázka najdete v [.Net ukázky][1] úložiště GitHub.
                      
## <a name="migrate-the-documents"></a>Migrace dokumentů

Zatímco definice kontejneru je rozšířena o vlastnost klíče oddílu, dokumenty v rámci kontejneru nejsou automaticky migrovány. To znamená, že `/_partitionKey` cesta vlastnosti klíče systémového oddílu není automaticky přidána do existujících dokumentů. Je třeba znovu rozdělit existující dokumenty čtením dokumentů, které byly vytvořeny bez `_partitionKey` klíče oddílu a přepsat je zpět s vlastností v dokumentech.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Přístup k dokumentům, které nemají klíč oddílu

Aplikace mohou přistupovat k existujícím dokumentům, které nemají klíč oddílu pomocí speciální vlastnosti systému s názvem "PartitionKey.None", to je hodnota nemigrovaných dokumentů. Tuto vlastnost můžete použít ve všech operacích CRUD a dotazů. Následující příklad ukazuje ukázku číst jeden dokument z NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Kompletní ukázku o tom, jak znovu rozdělit dokumenty, najdete v [tématu .Net ukázky][1] úložiště GitHub. 

## <a name="compatibility-with-sdks"></a>Kompatibilita se sadami SDK

Starší verze sad Azure Cosmos DB SDK, jako jsou V2.x.x a V1.x.x, nepodporují vlastnost klíči oddílu definovanou systémem. Takže při čtení definice kontejneru ze starší sady SDK, neobsahuje žádnou definici klíče oddílu a tyto kontejnery se budou chovat přesně jako dříve. Aplikace, které jsou vytvořeny se starší verzí sad SDK nadále pracovat s bez oddílů, jak je bez jakýchkoli změn. 

Pokud je migrovaný kontejner spotřebován nejnovější verzí sady SDK/V3 a začnete naplnění množiny systémově definovaného klíče oddílu v rámci nových dokumentů, nelze k těmto dokumentům již přistupovat (číst, aktualizovat, odstraňovat, dotazovat) tyto dokumenty ze starších sad SDK.

## <a name="known-issues"></a>Známé problémy

**Dotazování na počet položek, které byly vloženy bez klíče oddílu pomocí sady V3 SDK může zahrnovat vyšší spotřebu propustnost**

Pokud dotaz z V3 SDK pro položky, které jsou vloženy pomocí V2 SDK nebo položky `PartitionKey.None` vložené pomocí V3 SDK `PartitionKey.None` s parametrem, může počet dotazs spotřebovat více RU/s, pokud je parametr zadán v FeedOptions. Doporučujeme nezadávat parametr, `PartitionKey.None` pokud nejsou vloženy žádné další položky s klíč oddílu.

Pokud jsou vloženy nové položky s různými hodnotami pro klíč oddílu, `FeedOptions` dotazování na tyto položky počítá předáním příslušný klíč v nebude mít žádné problémy. Po vložení nové dokumenty s klíčem oddílu, pokud potřebujete dotaz pouze počet dokumentů bez hodnoty klíče oddílu, tento dotaz může opět vzniknout vyšší RU/s podobné pravidelné rozdělené kolekce.

## <a name="next-steps"></a>Další kroky

* [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
* [Jednotky požadavků v Azure Cosmos DB](request-units.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Práce s účtem Azure Cosmos](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration