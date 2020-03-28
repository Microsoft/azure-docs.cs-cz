---
title: Kurz pro nastavení globální distribuce pomocí rozhraní Azure Cosmos DB API pro MongoDB
description: Zjistěte, jak nastavit globální distribuci pomocí rozhraní API Azure Cosmos DB pro MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.openlocfilehash: b446697977395aa9bbbcf2192aa232fbc85a0b68
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75444668"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>Nastavení globální distribuované databáze pomocí rozhraní API Azure Cosmos DB pro MongoDB

V tomto článku ukážeme, jak pomocí portálu Azure nastavit globální distribuovanou databázi a připojit se k ní pomocí rozhraní API Azure Cosmos DB pro MongoDB.

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí [rozhraní API Azure Cosmos DB pro MongoDB](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>Ověření místního nastavení 
Jednoduchý způsob, jak zkontrolovat globální konfiguraci pomocí rozhraní API Cosmos DB pro MongoDB, je spustit příkaz *isMaster()* z prostředí Mongo.

V prostředí Mongo Shell:

   ```
      db.isMaster()
   ```
   
Příklad výsledků:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>Připojení k upřednostňované oblasti 

Rozhraní API Azure Cosmos DB pro MongoDB umožňuje určit předvolbu čtení vaší kolekce pro globálně distribuovanou databázi. Pro zajištění nízké latence čtení a globální vysoké dostupnosti doporučujeme nastavit předvolby kolekce pro čtení na *nearest* (nejbližší). Předvolba pro čtení *nearest* (nejbližší) je nakonfigurovaná pro čtení z nejbližší oblasti.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Pro aplikace s primární oblastí pro čtení i zápis a sekundární oblastí pro scénáře zotavení po havárii doporučujeme nastavit předvolby kolekce pro čtení na *secondary preferred* (preferovaná sekundární). Předvolba pro čtení *secondary preferred* (preferovaná sekundární) je nakonfigurovaná pro čtení ze sekundární oblasti v případě, že je primární oblast nedostupná.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

A konečně, oblasti pro čtení můžete zadat ručně. Provedete to nastavením značky region (oblast) v předvolbách pro čtení.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

To je vše, tento kurz je u konce. Informace o správě konzistence vašeho globálně replikovaného účtu najdete v tématu [Úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md). Další informace o fungování globální replikace databází ve službě Azure Cosmos DB najdete v tématu [Globální distribuce dat pomocí služby Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí rozhraní API Cosmos DB pro MongoDB

Teď můžete přejít k dalšímu kurzu, ve kterém se naučíte vyvíjet místně s využitím místního emulátoru služby Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Vývoj místně pomocí emulátoru Azure Cosmos DB](local-emulator.md)
