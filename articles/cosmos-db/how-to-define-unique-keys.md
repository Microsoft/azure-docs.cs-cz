---
title: Definujte jedinečné klíče pro kontejner Azure Cosmos
description: Další informace o definování jedinečné klíče pro kontejner Azure Cosmos
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: fb9872d2fd41066899ff9198915d573bfb4a0b84
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240971"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Definujte jedinečné klíče pro kontejner Azure Cosmos

Tento článek představuje různé způsoby, jak definovat [jedinečné klíče](unique-keys.md) při vytváření kontejneru Azure Cosmos. Je aktuálně možné k provedení této operace s využitím webu Azure portal nebo pomocí jedné ze sad SDK.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Vytvořit nový účet Azure Cosmos](create-sql-api-dotnet.md#create-account) nebo vyberte existující.

1. Otevřít **Průzkumník dat** podokně a vyberte kontejner, který chcete pracovat.

1. Klikněte na **nový kontejner**.

1. V **přidat kontejner** dialogového okna, klikněte na **+ přidat jedinečný klíč** přidáte položku jedinečný klíč.

1. Zadejte cesty omezení unique key

1. V případě potřeby přidejte další jedinečné klíče položky po kliknutí na **+ přidat jedinečný klíč**

![Snímek obrazovky omezení unique key položky na webu Azure portal](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-the-net-sdk-v2"></a>Používat .NET SDK V2

Při vytváření nového kontejneru pomocí [sady .NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/), `UniqueKeyPolicy` objekt lze použít k definování omezení jedinečných klíčů.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-java-sdk"></a>Použití sady Java SDK

Při vytváření nového kontejneru pomocí [sady Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb), `UniqueKeyPolicy` objekt lze použít k definování omezení jedinečných klíčů.

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

## <a name="use-the-nodejs-sdk"></a>Použití sady SDK pro Node.js

Při vytváření nového kontejneru pomocí [sady Node.js SDK](https://www.npmjs.com/package/@azure/cosmos), `UniqueKeyPolicy` objekt lze použít k definování omezení jedinečných klíčů.

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

## <a name="use-the-python-sdk"></a>Použít Python SDK

Při vytváření nového kontejneru pomocí [Python SDK](https://pypi.org/project/azure-cosmos/), omezení jedinečných klíčů se dá nastavit jako součást slovníku předán jako parametr.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            { 'paths': ['/firstName', '/lastName', '/emailAddress'] },
            { 'paths': ['/address/zipCode'] }
        ]
    }
})
```

## <a name="next-steps"></a>Další postup

- Další informace o [dělení](partition-data.md)
- Prozkoumejte [jak funguje indexování](index-overview.md)