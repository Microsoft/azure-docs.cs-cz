---
title: Informace o správě konfliktů mezi oblastmi ve službě Azure Cosmos DB
description: Informace o správě konfliktů ve službě Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 0dd0abae8ae912ebb7160d995672a97ce0bab69b
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243584"
---
# <a name="manage-conflicts-between-regions"></a>Správa konfliktů mezi oblastmi

V případě zápisů do více oblastí platí, že pokud dojde ke konfliktu dat, můžete ho vyřešit pomocí různých zásad řešení konfliktů. Tento článek popisuje, jak spravovat zásady řešení konfliktů s využitím různých jazyků a platforem.

## <a name="create-a-custom-conflict-resolution-policy"></a>Vytvoření vlastní zásady řešení konfliktů

Tyto ukázky předvádějí, jak nastavit kontejner s vlastní zásadou řešení konfliktů. Tyto konflikty se zobrazí v informačním kanálu konfliktů.

### <a id="create-custom-conflict-resolution-policy-dotnet"></a>.NET

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

### <a id="create-custom-conflict-resolution-policy-java-async"></a>Java Async

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-java-sync"></a>Java Sync

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```

### <a id="create-custom-conflict-resolution-policy-javascript"></a>Node.js/JavaScript/TypeScript

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

### <a id="create-custom-conflict-resolution-policy-python"></a>Python

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

## <a name="create-a-custom-conflict-resolution-policy-with-stored-procedure"></a>Vytvoření vlastní zásady řešení konfliktů s využitím uložené procedury

Tyto ukázky předvádějí, jak nastavit kontejner s vlastní zásadou řešení konfliktů s využitím uložené procedury, která konflikt vyřeší. Tyto konflikty se **nezobrazí** v informačním kanálu konfliktů, pokud ve vaší uložené proceduře nedojde k chybě.

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>.NET

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
```

Po vytvoření kontejneru bude potřeba vytvořit uloženou proceduru `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-async"></a>Java Async

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

Po vytvoření kontejneru bude potřeba vytvořit uloženou proceduru `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-sync"></a>Java Sync

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```

Po vytvoření kontejneru bude potřeba vytvořit uloženou proceduru `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>Node.js/JavaScript/TypeScript

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

Po vytvoření kontejneru bude potřeba vytvořit uloženou proceduru `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>Python

```python

```

Po vytvoření kontejneru bude potřeba vytvořit uloženou proceduru `resolver`.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Vytvoření zásady řešení konfliktů Poslední zapisovatel vyhrává

Tyto ukázky předvádějí, jak nastavit kontejner se zásadou řešení konfliktů Poslední zapisovatel vyhrává. Pokud cesta není zadaná nebo je neplatná, jako výchozí hodnota se použije vlastnost `_ts` (pole časového razítka). Tyto konflikty se **nezobrazí** v informačním kanálu konfliktů.

### <a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>.NET

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/regionId",
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-lww-java-async"></a>Java Async

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy(conflictResolutionPath);
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-lww-java-sync"></a>Java Sync

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/regionId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```

### <a id="create-custom-conflict-resolution-policy-lww-javascript"></a>Node.js/JavaScript/TypeScript

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/regionId"
    }
  }
);
```

Pokud vynecháte vlastnost `conflictResolutionPath`, jako výchozí hodnota se použije vlastnost `_ts`.

### <a id="create-custom-conflict-resolution-policy-lww-python"></a>Python

```python
udp_collection = {
                'id': self.udp_collection_name,
                'conflictResolutionPolicy': {
                    'mode': 'Custom',
                    'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
                    }
                }
udp_collection = self.try_create_document_collection(create_client, database, udp_collection)
```

## <a name="read-from-conflict-feed"></a>Čtení z informačního kanálu konfliktů

Tyto ukázky předvádějí, jak číst z informačního kanálu konfliktů kontejneru. V informačním kanálu konfliktů se zobrazí pouze konflikty, které se nevyřešily automaticky.

### <a id="read-from-conflict-feed-dotnet"></a>.NET

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

### <a id="read-from-conflict-feed-java-async"></a>Java Async

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-java-sync"></a>Java Sync

```java
Iterator<Conflict> conflictsIterartor = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterartor.hasNext()) {
    Conflict conflict = conflictsIterartor.next();
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-javascript"></a>Node.js/JavaScript/TypeScript

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterartor = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterartor, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Další kroky

Teď se můžete přesunout k učení následujících konceptů služby Cosmos DB:

* [Dělení a distribuce dat](partition-data.md)
* [Indexování ve službě Cosmos DB](indexing-policies.md)

