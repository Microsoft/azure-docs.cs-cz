---
title: Kurz globální distribuce pomocí rozhraní API služby Azure Cosmos DB pro MongoDB
description: Zjistěte, jak nastavit globální distribuce pomocí rozhraní API služby Azure Cosmos DB pro MongoDB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 5ae5923253575fc3dea6b90b599b9fa3d79a85b8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041369"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>Nastavení globální distribuovanou databázi s použitím rozhraní API služby Azure Cosmos DB pro MongoDB

V tomto článku vám ukážeme, jak pomocí webu Azure portal nastavit globální distribuovaná databáze a připojte se k němu pomocí rozhraní API služby Azure Cosmos DB pro MongoDB.

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí [rozhraní API služby Azure Cosmos DB pro MongoDB](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>Ověření regionálního nastavení 
Jednoduchý způsob, jak zkontrolovat globální konfiguraci s Cosmos DB přes rozhraní API pro MongoDB, je spustit *isMaster()* příkazu v prostředí Mongo Shell.

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

## <a name="connecting-to-a-preferred-region"></a>Připojení k preferované oblasti 

Azure Cosmos DB API pro MongoDB umožňuje určit vaše předvolby kolekce pro čtení pro globálně distribuované databáze. Pro zajištění nízké latence čtení a globální vysoké dostupnosti doporučujeme nastavit předvolby kolekce pro čtení na *nearest* (nejbližší). Předvolba pro čtení *nearest* (nejbližší) je nakonfigurovaná pro čtení z nejbližší oblasti.

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

## <a name="next-steps"></a>Další postup

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí rozhraní API služby Cosmos DB pro MongoDB

Teď můžete přejít k dalšímu kurzu, ve kterém se naučíte vyvíjet místně s využitím místního emulátoru služby Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Vývoj v místním prostředí pomocí emulátoru služby Azure Cosmos DB](local-emulator.md)
