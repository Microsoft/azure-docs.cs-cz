---
title: Vytvoření kontejnerů Azure Cosmos s velkým klíčem oddílu
description: Zjistěte, jak vytvořit kontejner v Azure Cosmos DB s velkým klíčem oddílu pomocí portálu Azure a různých sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7184a6b85e93c41dfe914813301a4b1a0c88f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887678"
---
# <a name="create-containers-with-large-partition-key"></a>Vytvoření kontejnerů s velkým klíčem oddílu

Azure Cosmos DB používá schéma dělení založené na hash k dosažení horizontální škálování dat. Všechny kontejnery Azure Cosmos vytvořené před 3 května 2019 použít funkci hash, která vypočítá hash na základě prvních 100 bajtů klíče oddílu. Pokud existuje více klíčů oddílů, které mají stejné první 100 bajtů, pak tyto logické oddíly jsou považovány za stejný logický oddíl službou. To může vést k problémům, jako je kvóta velikosti oddílu je nesprávné a jedinečné indexy jsou použity v klíčích oddílu. K vyřešení tohoto problému jsou zavedeny velké klíče oddílů. Azure Cosmos DB teď podporuje klíče velkých oddílů s hodnotami až 2 KB.

Velké klíče oddílů jsou podporovány pomocí funkce rozšířené verze funkce hash, která může generovat jedinečnou hash z velkých klíčů oddílů až po 2 kB. Tato verze hash je také doporučeno pro scénáře s vysokou klíč oddílu mohutnost bez ohledu na velikost klíče oddílu. Mohutnost klíče oddílu je definována jako počet jedinečných logických oddílů, například v pořadí ~30000 logických oddílů v kontejneru. Tento článek popisuje, jak vytvořit kontejner s velkým klíčem oddílu pomocí portálu Azure a různých sad SDK.

## <a name="create-a-large-partition-key-azure-portal"></a>Vytvoření velkého klíče oddílu (portál Azure)

Chcete-li vytvořit velký klíč oddílu, při vytváření nového kontejneru pomocí portálu Azure, zkontrolujte, že **můj klíč oddílu je větší než 100 bajtů** možnost. Zrušte zaškrtnutí políčka, pokud nepotřebujete velké klíče oddílů nebo pokud máte aplikace spuštěné ve verzi sad SDK starší než 1.18.

![Vytvoření velkých klíčů oddílů pomocí portálu Azure](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Vytvoření velkého klíče oddílu (PowerShell)

Chcete-li vytvořit kontejner s podporou velkých klíčů oddílu, podívejte se,

* [Vytvoření kontejneru Azure Cosmos s velkou velikostí klíče oddílu](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Vytvoření velkého klíče oddílu (.Net SDK)

Chcete-li vytvořit kontejner s velkým klíčem oddílu pomocí `PartitionKeyDefinitionVersion.V2` sady .NET SDK, zadejte vlastnost. Následující příklad ukazuje, jak určit Vlastnost Version v rámci objektu PartitionKeyDefinition a nastavit ji na PartitionKeyDefinitionVersion.V2.

### <a name="v3-net-sdk"></a>v3 sada SDK .NET

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

### <a name="v2-net-sdk"></a>v2 Sada SDK .NET

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

## <a name="supported-sdk-versions"></a>Podporované verze sady SDK

Velké klíče oddílů jsou podporovány následujícími minimálními verzemi sad SDK:

|Typ sady SDK  | Minimální verze   |
|---------|---------|
|.Net     |    1.18     |
|Synchronizace javy     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | verze vyšší `2017-05-03` než `x-ms-version` pomocí hlavičky požadavku.|
| Šablona Resource Manageru | verze 2 pomocí `"version":2` vlastnosti `partitionKey` v rámci objektu. |

V současné době nelze použít kontejnery s velkým klíčem oddílu v rámci Power BI a Azure Logic Apps. Můžete použít kontejnery bez klíče velkého oddílu z těchto aplikací.

## <a name="next-steps"></a>Další kroky

* [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
* [Jednotky požadavků v Azure Cosmos DB](request-units.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Práce s účtem Azure Cosmos](account-overview.md)
