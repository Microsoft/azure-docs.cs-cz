---
title: Kurz globální distribuci Azure Cosmos DB pro rozhraní MongoDB API
description: Zjistěte, jak nastavit globální distribuci služby Azure Cosmos DB pomocí rozhraní MongoDB API.
services: cosmos-db
keywords: global distribution, MongoDB
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 27fa9849c13de151f6922e829514cc8838f295ea
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874754"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-mongodb-api"></a>Nastavení globální distribuce služby Azure Cosmos DB pomocí rozhraní MongoDB API

V tomto článku si ukážeme, jak pomocí webu Azure Portal nastavit globální distribuci služby Azure Cosmos DB a pak se k ní připojit pomocí rozhraní MongoDB API.

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí rozhraní [MongoDB API](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup-using-the-mongodb-api"></a>Ověření regionálního nastavení pomocí rozhraní MongoDB API
Nejjednodušším způsobem, jak pečlivě zkontrolovat globální konfiguraci v rámci rozhraní API pro MongoDB, je spustit příkaz *isMaster()* v prostředí Mongo Shell.

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

## <a name="connecting-to-a-preferred-region-using-the-mongodb-api"></a>Připojení k preferované oblasti pomocí rozhraní MongoDB API

U globálně distribuované databáze umožňuje rozhraní MongoDB API zadat předvolby kolekce pro čtení. Pro zajištění nízké latence čtení a globální vysoké dostupnosti doporučujeme nastavit předvolby kolekce pro čtení na *nearest* (nejbližší). Předvolba pro čtení *nearest* (nejbližší) je nakonfigurovaná pro čtení z nejbližší oblasti.

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
> * Konfigurace globální distribuce pomocí rozhraní SQL API

Teď můžete přejít k dalšímu kurzu, ve kterém se naučíte vyvíjet místně s využitím místního emulátoru služby Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Místní vývoj s využitím emulátoru](local-emulator.md)
