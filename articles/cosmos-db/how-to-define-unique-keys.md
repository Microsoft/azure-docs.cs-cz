---
title: Definování jedinečných klíčů pro kontejner Azure Cosmos
description: Naučte se definovat jedinečné klíče pro kontejner Azure Cosmos pomocí Azure Portal, PowerShellu, .NET, Java a různých dalších sad SDK.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/02/2019
ms.author: thweiss
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 55fc5222c1c245c56ba0a26caa816c5c845147c1
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336618"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Definování jedinečných klíčů pro kontejner Azure Cosmos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek nabízí různé způsoby definování [jedinečných klíčů](unique-keys.md) při vytváření kontejneru Azure Cosmos. Tuto operaci je teď možné provést buď pomocí Azure Portal nebo prostřednictvím jedné ze sad SDK.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account) nebo vyberte nějaký existující.

1. Otevřete podokno **Průzkumník dat** a vyberte kontejner, na kterém chcete pracovat.

1. Klikněte na **Nový kontejner**.

1. V dialogovém okně **Přidat kontejner** kliknutím na **+ přidat jedinečný klíč** přidejte jedinečnou položku klíče.

1. Zadejte cesty k omezení jedinečného klíče.

1. V případě potřeby přidejte více jedinečných klíčových položek kliknutím na **+ přidat jedinečný klíč** .

    :::image type="content" source="./media/how-to-define-unique-keys/unique-keys-portal.png" alt-text="Snímek obrazovky s jedinečnou položkou omezení klíčů v Azure Portal":::

## <a name="use-powershell"></a>Použití prostředí PowerShell

Vytvoření kontejneru s jedinečnými klíči najdete v tématu [vytvoření kontejneru Azure Cosmos s jedinečným klíčem a hodnotou TTL](manage-with-powershell.md#create-container-unique-key-ttl) .

## <a name="use-the-net-sdk"></a>Použití sady .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

Při vytváření nového kontejneru pomocí [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)se `UniqueKeyPolicy` dá objekt použít k definování jedinečných omezení klíčů.

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Při vytváření nového kontejneru pomocí [.NET SDK V3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)použijte rozhraní API Fluent sady SDK k deklaraci jedinečných klíčů stručným a čitelným způsobem.

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
---

## <a name="use-the-java-sdk"></a>Použití sady Java SDK

Při vytváření nového kontejneru pomocí [sady Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb), `UniqueKeyPolicy` lze objekt použít k definování jedinečných omezení klíčů.

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

## <a name="use-the-nodejs-sdk"></a>Použití sady Node.js SDK

Při vytváření nového kontejneru pomocí [ sadyNode.js SDK](https://www.npmjs.com/package/@azure/cosmos)se `UniqueKeyPolicy` dá objekt použít k definování jedinečných omezení klíčů.

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

Při vytváření nového kontejneru pomocí [sady Python SDK](https://pypi.org/project/azure-cosmos/)lze zadat omezení jedinečnosti klíčů jako součást slovníku předaného jako parametr.

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

- Další informace o [dělení](partitioning-overview.md)
- Prozkoumejte, [Jak funguje indexování](index-overview.md)
