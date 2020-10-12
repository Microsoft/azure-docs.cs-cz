---
title: Vytvoření kontejnerů Azure Cosmos s velkým klíčem oddílu
description: Naučte se, jak vytvořit kontejner ve Azure Cosmos DB s velkým klíčem oddílu pomocí Azure Portal a různých sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/28/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: 59781f9b86764d0982e1612f9671e8f0919ed490
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89009504"
---
# <a name="create-containers-with-large-partition-key"></a>Vytváření kontejnerů s velkým klíčem oddílu

Azure Cosmos DB používá schéma dělení na základě hodnoty hash k zajištění horizontálního škálování dat. Všechny kontejnery Azure Cosmos vytvořené před může 3 2019 použít funkci hash, která vypočítá hodnotu hash na základě prvních 100 bajtů klíče oddílu. Pokud existuje více klíčů oddílů, které mají stejné prvních 100 bajtů, pak se tyto logické oddíly považují za stejný logický oddíl služby. To může vést k problémům, jako je nesprávná kvóta velikosti oddílu a jedinečné indexy použité v rámci klíčů oddílu. Pro vyřešení tohoto problému jsou představeny klíče s velkými oddíly. Azure Cosmos DB teď podporuje velké klíče oddílů s hodnotami až do 2 KB.

Klíče s velkými oddíly se podporují pomocí funkce Rozšířené verze funkce hash, která může vygenerovat jedinečnou hodnotu hash z klíčů velkých oddílů až do 2 KB. Tato verze algoritmu hash se také doporučuje u scénářů s vysokou mohutnou klíč oddílu bez ohledu na velikost klíče oddílu. Mohutnost klíče oddílu je definovaná jako počet jedinečných logických oddílů, například v pořadí logických oddílů ~ 30000 v kontejneru. Tento článek popisuje, jak vytvořit kontejner s velkým klíčem oddílu pomocí Azure Portal a různých sad SDK.

## <a name="create-a-large-partition-key-azure-portal"></a>Vytvoření klíče s velkým oddílem (Azure Portal)

Pokud chcete vytvořit velký klíč oddílu, když vytvoříte nový kontejner pomocí Azure Portal, podívejte se, jestli **je klíč oddílu můj oddíl větší než 100 bajtů** . Zrušte zaškrtnutí políčka, pokud nepotřebujete klíče velkých oddílů, nebo pokud máte aplikace spuštěné v sadách SDK verze starší než 1,18.

:::image type="content" source="./media/large-partition-keys/large-partition-key-with-portal.png" alt-text="Vytváření velkých klíčů oddílů pomocí Azure Portal":::

## <a name="create-a-large-partition-key-powershell"></a>Vytvoření klíče s velkým oddílem (PowerShell)

Pokud chcete vytvořit kontejner s podporou klíče velkých oddílů, přejděte na

* [Vytvoření kontejneru Azure Cosmos s velkou velikostí klíče oddílu](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Vytvoření klíče s velkým oddílem (.NET SDK)

Chcete-li vytvořit kontejner s velkým klíčem oddílu pomocí sady .NET SDK, zadejte `PartitionKeyDefinitionVersion.V2` vlastnost. Následující příklad ukazuje, jak určit vlastnost verze v objektu PartitionKeyDefinition a nastavit ji na PartitionKeyDefinitionVersion. v2.

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```
---

## <a name="supported-sdk-versions"></a>Podporované verze sady SDK

V následujících minimálních verzích sad SDK jsou podporovány klíče s velkými oddíly:

|Typ sady SDK  | Minimální verze   |
|---------|---------|
|.Net     |    1,18     |
|Synchronizace Java     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | verze je vyšší než pomocí `2017-05-03` `x-ms-version` hlavičky Request.|
| Šablona Resource Manageru | verze 2 pomocí `"version":2` vlastnosti v rámci `partitionKey` objektu. |

V současné době nelze v nástroji Power BI a Azure Logic Apps použít kontejnery s velkým klíčem oddílu. Z těchto aplikací můžete použít kontejnery bez klíče oddílu.

## <a name="next-steps"></a>Další kroky

* [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
* [Jednotky žádostí ve službě Azure Cosmos DB](request-units.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Práce s účtem Azure Cosmos](account-overview.md)
