---
title: Definování jedinečných klíčů pro kontejner Azure Cosmos
description: Zjistěte, jak definovat jedinečné klíče pro kontejner Azure Cosmos pomocí portálu Azure, PowerShellu, .Net, Java a různých dalších sad SDK.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: fa62495a7b51c9a06a91102299378c15e811eae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872107"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Definování jedinečných klíčů pro kontejner Azure Cosmos

Tento článek představuje různé způsoby definování [jedinečných klíčů](unique-keys.md) při vytváření kontejneru Azure Cosmos. V současné době je možné provést tuto operaci pomocí portálu Azure nebo prostřednictvím jedné z sad SDK.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. [Vytvořte si nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account) nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte kontejner, na kterém chcete pracovat.

1. Klikněte na **Nový kontejner**.

1. V dialogovém okně **Přidat kontejner** klikněte na + Přidat **jedinečný klíč** a přidejte jedinečnou položku klíče.

1. Zadejte cestu (cesty) jedinečného omezení klíče.

1. V případě potřeby přidejte další jedinečné klíčové položky kliknutím na **+ Přidat jedinečný klíč**

    ![Snímek obrazovky s položkou jedinečného omezení klíče na webu Azure Portal](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-powershell"></a>Použití prostředí Powershell

Pokud chcete vytvořit kontejner s jedinečnými klíči, podívejte se na [vytvoření kontejneru Azure Cosmos s jedinečným klíčem a ttl](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="use-the-net-sdk-v2"></a>Použití sady .NET SDK V2

Při vytváření nového kontejneru pomocí [sady .NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)lze `UniqueKeyPolicy` objekt použít k definování jedinečných omezení klíče.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-net-sdk-v3"></a>Použití sady .NET SDK V3

Při vytváření nového kontejneru pomocí [sady .NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)použijte rozhraní SDK fluent API k deklarování jedinečných klíčů výstižným a čitelným způsobem.

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>Použití sady Java SDK

Při vytváření nového kontejneru pomocí sady `UniqueKeyPolicy` Java [SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)lze objekt použít k definování jedinečných omezení klíče.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>Použití sady SDK Node.js

Při vytváření nového kontejneru pomocí [sady SDK Node.js](https://www.npmjs.com/package/@azure/cosmos)lze `UniqueKeyPolicy` objekt použít k definování jedinečných omezení klíče.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>Použití sady Python SDK

Při vytváření nového kontejneru pomocí [sady Python SDK](https://pypi.org/project/azure-cosmos/)lze jako součást slovníku předaného jako parametr zadat jedinečná omezení klíče.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>Další kroky

- Další informace o [rozdělení](partition-data.md)
- [Zjistěte, jak funguje indexování](index-overview.md)
