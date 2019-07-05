---
title: Vytvoření kontejnerů Azure Cosmos s klíči velkých oddílů pomocí webu Azure portal a různých sadách SDK.
description: Zjistěte, jak vytvořit kontejner ve službě Azure Cosmos DB s klíči velkých oddílů pomocí webu Azure portal a různé sady SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: bd1697378e5db0432d181f9f688ccc2468b306e7
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566025"
---
# <a name="create-containers-with-large-partition-key"></a>Vytvoření kontejnerů s klíči velkých oddílů

Azure Cosmos DB používá k dosažení horizontální škálování dat schéma dělení podle algoritmu hash. Všechny kontejnery Azure Cosmos vytvořené před 3 května 2019 pomocí funkce hash, který vypočítává hash založené na prvních 100 bajtů klíč oddílu. Pokud existuje více klíčů oddílů, které mají stejné prvních 100 bajtů, pak tyto logické oddíly se považují za stejného logického oddílu službou. To může vést k problémy, jako jsou kvóty velikost oddílu byla zadána nesprávná a jedinečné indexy, které se používají klíče oddílů. Chcete-li vyřešit tento problém jsou zavedeny klíči velkých oddílů. Azure Cosmos DB teď podporuje klíči velkých oddílů s hodnotami až 2 KB.

Velkých oddílů, které jsou podporovány klíče pomocí funkce rozšířenou verzi funkce hash, která mohou generovat jedinečnou hodnotu hash z velkých oddílů klíčů až 2 KB. Tato hodnota hash verze se také doporučuje pro scénáře s vysokou oddílu mohutnost klíče bez ohledu na velikost klíče oddílu. Mohutnost klíče oddílu je definován jako počet jedinečných logické oddíly, třeba v pořadí ~ 30000 logické oddíly v kontejneru. Tento článek popisuje, jak vytvořit kontejner s klíči velkých oddílů pomocí webu Azure portal a různé sady SDK. 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>Vytvořte klíč velkých oddílů (.Net SDK V2)

Chcete-li vytvořit kontejner s klíči velkých oddílů pomocí .NET SDK, zadejte `PartitionKeyDefinitionVersion.V2` vlastnost. Následující příklad ukazuje, jak zadat vlastnost Version v rámci objektu PartitionKeyDefinition a nastavte ho na PartitionKeyDefinitionVersion.V2.

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

## <a name="create-a-large-partition-key-azure-portal"></a>Vytvořte klíč velkých oddílů (portál Azure portal) 

Chcete-li vytvořit klíči velkých oddílů, když vytvoříte nový kontejner pomocí webu Azure portal, zkontrolujte **Můj klíč oddílu je větší než 100 bajtů** možnost. Ve výchozím nastavení jsou nové kontejnery přihlášení ke službě pomocí klíči velkých oddílů. Pokud už nepotřebujete klíči velkých oddílů, nebo pokud máte aplikace běžící na verzi sady SDK, které jsou starší než 1.18, zrušte zaškrtnutí políčka.

![Vytvoření klíči velkých oddílů pomocí webu Azure portal](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Vytvořte klíč velkých oddílů (PowerShell)

Pokud chcete vytvořit kontejner s klíči velkých oddílů pomocí Powershellu, zahrňte `"version" = 2` pro `partitionKey` objektu.

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

Klíči velkých oddílů je podporovaná v následujících minimálních verzích sady SDK:

|Typ sady SDK  | Minimální verze   |
|---------|---------|
|.Net     |    1.18     |
|Synchronizace Java     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | vyšší než verze `2017-05-03` pomocí `x-ms-version` hlavičky žádosti.|

V současné době nelze pomocí kontejnerů s klíči velkých oddílů v rámci v Power BI a Azure Logic Apps. Pomocí kontejnerů bez klíči velkých oddílů z těchto aplikací.

## <a name="next-steps"></a>Další postup

* [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
* [Jednotky žádosti v Azure Cosmos DB](request-units.md)
* [Zřizování propustnosti na kontejnerech a databází](set-throughput.md)
* [Práce s účtem Azure Cosmos](account-overview.md)


