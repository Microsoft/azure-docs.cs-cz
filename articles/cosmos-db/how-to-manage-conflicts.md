---
title: Správa konfliktů mezi oblastmi v Azure Cosmos DB
description: Přečtěte si, jak spravovat konflikty v Azure Cosmos DB vytvořením zásad pro službu WINS pro poslední zápis nebo vlastní řešení konfliktů.
author: anfeldma-ms
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/11/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 2ebe4fbf776ffb3b4568f996f20ba721b0cc5fca
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330510"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Správa zásad řešení konfliktů v Azure Cosmos DB

V případě zápisů ve více oblastech může dojít ke konfliktům, pokud více klientů zapisuje ke stejné položce. Pokud dojde ke konfliktu, můžete konflikt vyřešit pomocí různých zásad řešení konfliktů. Tento článek popisuje, jak spravovat zásady řešení konfliktů.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Vytvoření zásad řešení konfliktů pro poslední zápis a službu WINS

V těchto ukázkách se dozvíte, jak nastavit kontejner pomocí zásad řešení konfliktů pro poslední zápis a službu WINS. Výchozí cesta pro poslední zapisovač – WINS je pole časového razítka nebo `_ts` vlastnost. Pro rozhraní SQL API to může být také nastaveno na uživatelsky definovanou cestu s číselným typem. V konfliktu je nejvyšší hodnota služba WINS. Pokud cesta není nastavena nebo je neplatná, nastaví se jako výchozí `_ts` . Konflikty vyřešené s touto zásadou se v informačním kanálu konfliktu nezobrazují. Tuto zásadu můžou používat všechna rozhraní API.

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/myCustomId",
      },
  });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.lwwCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.LastWriterWins,
            ResolutionPath = "/myCustomId",
        }
    });
```
---

### <a name="java-v4-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-javav4"></a> Sada Java v4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConflictResolutionLWWAsync)]

# <a name="sync"></a>[Synchronizovat](#tab/api-sync)

   Sada Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConflictResolutionLWWSync)]

--- 

### <a name="java-v2-sdks"></a><a id="create-custom-conflict-resolution-policy-lww-javav2"></a>Sady SDK pro Java v2

# <a name="async-java-v2-sdk"></a>[Async Java v2 SDK](#tab/async)

[Async Java v2 SDK](sql-api-sdk-async-java.md)   (Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="sync-java-v2-sdk"></a>[Synchronizace sady Java v2 SDK](#tab/sync)

[Synchronizace sady Java v2 SDK](sql-api-sdk-java.md)   (Maven [com. Microsoft. Azure:: Azure-DocumentDB](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/myCustomId"
    }
  }
);
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-python"></a>Python SDK

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'LastWriterWins',
        'conflictResolutionPath': '/myCustomId'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>Vytvoření vlastních zásad řešení konfliktů pomocí uložené procedury

Tyto ukázky předvádějí, jak nastavit kontejner s vlastní zásadou řešení konfliktů s využitím uložené procedury, která konflikt vyřeší. Tyto konflikty se v informačním kanálu neprojeví, pokud se v uložené proceduře neobjeví chyba. Po vytvoření zásady s kontejnerem je potřeba vytvořit uloženou proceduru. Příklad obsahuje ukázku sady .NET SDK. Tato zásada se podporuje jenom v rozhraních API Core (SQL).

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>Ukázka uložené procedury vlastního řešení konfliktů

Uložené procedury řešení Custom konfliktů by se měly implementovat pomocí signatury funkce uvedené níže. Název funkce se nemusí shodovat s názvem použitým při registraci uložené procedury s kontejnerem, ale zjednodušuje pojmenování. Zde je popis parametrů, které musí být pro tuto uloženou proceduru implementovány.

- **incomingItem**: položka, která je vložena nebo aktualizována v potvrzení, které generuje konflikty. Má hodnotu null pro operace odstranění.
- **existingItem**: aktuálně potvrzená položka. Tato hodnota je v aktualizaci jiná než null a pro vložení nebo odstranění je null.
- neoznačovatelné **položky: logická**hodnota označující, jestli je incomingItem v konfliktu s dříve odstraněnou položkou. V případě hodnoty true je existingItem také null.
- **conflictingItems**: pole zapsané verze všech položek v kontejneru, které jsou v konfliktu s INCOMINGITEM na ID nebo jakékoli jiné jedinečné vlastnosti indexu.

> [!IMPORTANT]
> Stejně jako u jakékoli uložené procedury má vlastní procedura řešení konfliktů přístup k jakýmkoli datům se stejným klíčem oddílu a může provést jakoukoli operaci vložení, aktualizace nebo odstranění pro vyřešení konfliktů.

Tato ukázková uložená procedura vyřeší konflikty výběrem nejnižší hodnoty z `/myCustomId` cesty.

```javascript
function resolver(incomingItem, existingItem, isTombstone, conflictingItems) {
  var collection = getContext().getCollection();

  if (!incomingItem) {
      if (existingItem) {

          collection.deleteDocument(existingItem._self, {}, function (err, responseOptions) {
              if (err) throw err;
          });
      }
  } else if (isTombstone) {
      // delete always wins.
  } else {
      if (existingItem) {
          if (incomingItem.myCustomId > existingItem.myCustomId) {
              return; // existing item wins
          }
      }

      var i;
      for (i = 0; i < conflictingItems.length; i++) {
          if (incomingItem.myCustomId > conflictingItems[i].myCustomId) {
              return; // existing conflict item wins
          }
      }

      // incoming item wins - clear conflicts and replace existing with incoming.
      tryDelete(conflictingItems, incomingItem, existingItem);
  }

  function tryDelete(documents, incoming, existing) {
      if (documents.length > 0) {
          collection.deleteDocument(documents[0]._self, {}, function (err, responseOptions) {
              if (err) throw err;

              documents.shift();
              tryDelete(documents, incoming, existing);
          });
      } else if (existing) {
          collection.replaceDocument(existing._self, incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      } else {
          collection.createDocument(collection.getSelfLink(), incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      }
  }
}
```

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });

//Create the stored procedure
await clients[0].CreateStoredProcedureAsync(
UriFactory.CreateStoredProcedureUri(this.databaseName, this.udpCollectionName, "resolver"), new StoredProcedure
{
    Id = "resolver",
    Body = File.ReadAllText(@"resolver.js")
});
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.udpCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom,
            ResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver")
        }
    });

await container.Scripts.CreateStoredProcedureAsync(
    new StoredProcedureProperties("resolver", File.ReadAllText(@"resolver.js"))
);
```
---

### <a name="java-v4-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javav4"></a> Sada Java v4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConflictResolutionSprocAsync)]

# <a name="sync"></a>[Synchronizovat](#tab/api-sync)

   Sada Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConflictResolutionSprocSync)]

--- 

### <a name="java-v2-sdks"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javav2"></a>Sady SDK pro Java v2

# <a name="async-java-v2-sdk"></a>[Async Java v2 SDK](#tab/async)

[Async Java v2 SDK](sql-api-sdk-async-java.md)   (Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="sync-java-v2-sdk"></a>[Synchronizace sady Java v2 SDK](#tab/sync)

[Synchronizace sady Java v2 SDK](sql-api-sdk-java.md)   (Maven [com. Microsoft. Azure:: Azure-DocumentDB](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```
---

Po vytvoření kontejneru je nutné vytvořit `resolver` uloženou proceduru.

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

Po vytvoření kontejneru je nutné vytvořit `resolver` uloženou proceduru.

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>Python SDK

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom',
        'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

Po vytvoření kontejneru je nutné vytvořit `resolver` uloženou proceduru.

## <a name="create-a-custom-conflict-resolution-policy"></a>Vytvoření vlastní zásady řešení konfliktů

Tyto ukázky předvádějí, jak nastavit kontejner s vlastní zásadou řešení konfliktů. Tyto konflikty se zobrazí v informačním kanálu o konfliktech.

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.manualCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom
        }
    });
```
---

### <a name="java-v4-sdk"></a><a id="create-custom-conflict-resolution-policy-javav4"></a> Sada Java v4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConflictResolutionCustomAsync)]

# <a name="sync"></a>[Synchronizovat](#tab/api-sync)

   Sada Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConflictResolutionCustomSync)]

--- 

### <a name="java-v2-sdks"></a><a id="create-custom-conflict-resolution-policy-javav2"></a>Sady SDK pro Java v2

# <a name="async-java-v2-sdk"></a>[Async Java v2 SDK](#tab/async)

[Async Java v2 SDK](sql-api-sdk-async-java.md)   (Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="sync-java-v2-sdk"></a>[Synchronizace sady Java v2 SDK](#tab/sync)

[Synchronizace sady Java v2 SDK](sql-api-sdk-java.md)   (Maven [com. Microsoft. Azure:: Azure-DocumentDB](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-python"></a>Python SDK

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
    'id': self.manual_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom'
    }
}
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="read-from-conflict-feed"></a>Čtení z informačního kanálu konfliktů

Tyto ukázky předvádějí, jak číst z informačního kanálu konfliktů kontejneru. Konflikty se v informačním kanálu zobrazí jenom v případě, že se nevyřešily automaticky nebo pokud používáte zásady vlastního konfliktu.

### <a name="net-sdk"></a><a id="read-from-conflict-feed-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
FeedIterator<ConflictProperties> conflictFeed = container.Conflicts.GetConflictQueryIterator();
while (conflictFeed.HasMoreResults)
{
    FeedResponse<ConflictProperties> conflicts = await conflictFeed.ReadNextAsync();
    foreach (ConflictProperties conflict in conflicts)
    {
        // Read the conflicted content
        MyClass intendedChanges = container.Conflicts.ReadConflictContent<MyClass>(conflict);
        MyClass currentState = await container.Conflicts.ReadCurrentAsync<MyClass>(conflict, new PartitionKey(intendedChanges.MyPartitionKey));

        // Do manual merge among documents
        await container.ReplaceItemAsync<MyClass>(intendedChanges, intendedChanges.Id, new PartitionKey(intendedChanges.MyPartitionKey));

        // Delete the conflict
        await container.Conflicts.DeleteAsync(conflict, new PartitionKey(intendedChanges.MyPartitionKey));
    }
}
```
---

### <a name="java-v2-sdks"></a><a id="read-from-conflict-feed-javav2"></a>Sady SDK pro Java v2

# <a name="async-java-v2-sdk"></a>[Async Java v2 SDK](#tab/async)

[Async Java v2 SDK](sql-api-sdk-async-java.md)   (Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```
# <a name="sync-java-v2-sdk"></a>[Synchronizace sady Java v2 SDK](#tab/sync)

[Synchronizace sady Java v2 SDK](sql-api-sdk-java.md)   (Maven [com. Microsoft. Azure:: Azure-DocumentDB](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="read-from-conflict-feed-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a name="python"></a><a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Další kroky

Přečtěte si o následujících konceptech Azure Cosmos DB:

- [Globální distribuce – pod pokličkou](global-dist-under-the-hood.md)
- [Jak v aplikacích nakonfigurovat více hlavních serverů](how-to-multi-master.md)
- [Konfigurace klientů pro vícedomé služby](how-to-manage-database-account.md#configure-multiple-write-regions)
- [Přidat nebo odebrat oblasti z Azure Cosmos DB účtu](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
- [Jak v aplikacích nakonfigurovat více hlavních serverů](how-to-multi-master.md).
- [Dělení a distribuce dat](partition-data.md)
- [Indexování ve službě Azure Cosmos DB](indexing-policies.md)
