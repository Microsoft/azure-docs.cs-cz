---
title: Pomocí Azure Portal a různých sad SDK vytvořte kontejnery Azure Cosmos s velkým klíčem oddílu.
description: Naučte se, jak vytvořit kontejner ve Azure Cosmos DB s velkým klíčem oddílu pomocí Azure Portal a různých sad SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: a1216daade2df832b606fceb648fca998c3fdec8
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300137"
---
# <a name="create-containers-with-large-partition-key"></a>Vytváření kontejnerů s velkým klíčem oddílu

Azure Cosmos DB používá schéma dělení na základě hodnoty hash k zajištění horizontálního škálování dat. Všechny kontejnery služby Azure Cosmos vytvořené před 3 2019 mohou používat funkci hash, která vypočítá hodnotu hash na základě prvních 100 bajtů klíče oddílu. Pokud existuje více klíčů oddílů, které mají stejné prvních 100 bajtů, pak se tyto logické oddíly považují za stejný logický oddíl služby. To může vést k problémům, jako je nesprávná kvóta velikosti oddílu a jedinečné indexy použité v rámci klíčů oddílu. Pro vyřešení tohoto problému jsou představeny klíče s velkými oddíly. Azure Cosmos DB teď podporuje velké klíče oddílů s hodnotami až do 2 KB.

Klíče s velkými oddíly se podporují pomocí funkce Rozšířené verze funkce hash, která může vygenerovat jedinečnou hodnotu hash z klíčů velkých oddílů až do 2 KB. Tato verze algoritmu hash se také doporučuje u scénářů s vysokou mohutnou klíč oddílu bez ohledu na velikost klíče oddílu. Mohutnost klíče oddílu je definovaná jako počet jedinečných logických oddílů, například v pořadí logických oddílů ~ 30000 v kontejneru. Tento článek popisuje, jak vytvořit kontejner s velkým klíčem oddílu pomocí Azure Portal a různých sad SDK. 

## <a name="create-a-large-partition-key-net-sdk"></a>Vytvoření klíče s velkým oddílem (.NET SDK)

Chcete-li vytvořit kontejner s velkým klíčem oddílu pomocí sady .NET SDK, zadejte `PartitionKeyDefinitionVersion.V2` vlastnost. Následující příklad ukazuje, jak určit vlastnost verze v objektu PartitionKeyDefinition a nastavit ji na PartitionKeyDefinitionVersion. v2.

### <a name="v3-net-sdk"></a>V3 .NET SDK

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2, 
    })
```

### <a name="v2-net-sdk"></a>v2 .NET SDK

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

## <a name="create-a-large-partition-key-azure-portal"></a>Vytvoření klíče s velkým oddílem (Azure Portal) 

Pokud chcete vytvořit velký klíč oddílu a při vytváření nového kontejneru pomocí Azure Portal, podívejte se, jestli **je klíč oddílu můj oddíl větší než 100 bajtů** . Ve výchozím nastavení se všechny nové kontejnery přihlásily k používání velkých klíčů oddílu. Zrušte zaškrtnutí políčka, pokud nepotřebujete klíče velkých oddílů, nebo pokud máte aplikace spuštěné v sadách SDK verze starší než 1,18.

![Vytváření velkých klíčů oddílů pomocí Azure Portal](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Vytvoření klíče s velkým oddílem (PowerShell)

Pokud chcete vytvořit kontejner s velkým klíčem oddílu pomocí PowerShellu, zahrňte `"version" = 2` do `partitionKey` objektu.

```azurepowershell-interactive
# Create a Cosmos SQL API container with large partition key support (version 2)
$resourceGroupName = "myResourceGroup"
$containerName = "mycosmosaccount" + "/sql/" + "myDatabase" + "/" + "myContainer"

# Container with large partition key support (version = 2)
$containerProperties = @{
  "resource"=@{
    "id"=$containerName;
    "partitionKey"=@{
        "paths"=@("/myPartitionKey");
        "kind"="Hash";
        "version" = 2
    };
    "indexingPolicy"=@{
        "indexingMode"="Consistent";
        "includedPaths"= @(@{
            "path"="/*"
        });
        "excludedPaths"= @(@{
            "path"="/myPathToNotIndex/*"
        })
    }
  }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerName -PropertyObject $containerProperties
```

## <a name="supported-sdk-versions"></a>Podporované verze sady SDK

V následujících minimálních verzích sad SDK jsou podporovány klíče s velkými oddíly:

|Typ sady SDK  | Minimální verze   |
|---------|---------|
|.Net     |    1,18     |
|Synchronizace Java     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | verze je `2017-05-03` `x-ms-version` vyšší než pomocí hlavičky Request.|

V současné době nelze v nástroji Power BI a Azure Logic Apps použít kontejnery s velkým klíčem oddílu. Z těchto aplikací můžete použít kontejnery bez klíče oddílu.

## <a name="next-steps"></a>Další kroky

* [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
* [Jednotky žádostí ve službě Azure Cosmos DB](request-units.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md)
* [Práce s účtem Azure Cosmos](account-overview.md)


