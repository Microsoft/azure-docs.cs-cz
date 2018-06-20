---
title: Kurz globální distribuce služby Azure Cosmos DB pro rozhraní Graph API | Microsoft Docs
description: Zjistěte, jak nastavit globální distribuci služby Azure Cosmos DB pomocí rozhraní Graph API.
services: cosmos-db
keywords: global distribution, graph, gremlin
author: luisbosquez
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 61004a735f731cfd1303cf40b6e60cba496e942a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763676"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-graph-api"></a>Nastavení globální distribuce služby Azure Cosmos DB pomocí rozhraní Graph API

V tomto článku si ukážeme, jak pomocí webu Azure Portal nastavit globální distribuci služby Azure Cosmos DB a pak se k ní připojit pomocí rozhraní Graph API.

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí rozhraní [Graph API](graph-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>Připojení k preferované oblasti pomocí rozhraní Graph API s použitím sady .NET SDK

Rozhraní Graph API je k dispozici jako knihovna rozšíření nad rámec rozhraní SQL API.

Aby mohly využívat [globální distribuci](distribute-data-globally.md), můžou mít klientské aplikace určený seřazený seznam preferovaných oblastí, které se mají použít k provádění operací s dokumenty. To je možné provést nastavením zásady připojení. V závislosti na konfiguraci účtu služby Azure Cosmos DB, aktuální regionální dostupnosti a zadaného seznamu preferencí zvolí sada SDK optimální koncový bod, který bude provádět operace čtení a zápisu.

Tento seznam preferencí se zadává při inicializaci připojení pomocí sad SDK. Sady SDK přijímají volitelný parametr PreferredLocations, což je seřazený seznam oblastí Azure.

* **Zápisy:** Sada SDK automaticky odesílá všechny operace zápisu do aktuální oblasti pro zápis.
* **Čtení:** Všechny operace čtení se odesílají do první dostupné oblasti v seznamu PreferredLocations. Pokud požadavek selže, klient přejde k další oblasti v seznamu atd. Sady SDK se pokouší číst pouze z oblastí uvedených v seznamu PreferredLocations. Takže pokud je například účet služby Cosmos DB dostupný ve třech oblastech, ale v seznamu PreferredLocations klienta jsou uvedené pouze dvě oblasti jen pro čtení, z oblasti pro zápis se číst nebude, a to ani v případě převzetí služeb při selhání.

Aplikace může ověřit aktuální koncový bod pro čtení a koncový bod pro zápis, které sada SDK zvolila, kontrolou dvou vlastností WriteEndpoint a ReadEndpoint, které jsou dostupné v sadě SDK verze 1.8 a novější. Pokud vlastnost PreferredLocations není nastavená, všechny požadavky se obsluhují z aktuální oblasti pro zápis.

### <a name="using-the-sdk"></a>Použití sady SDK

Například v sadě .NET SDK má parametr `ConnectionPolicy` pro konstruktor `DocumentClient` vlastnost `PreferredLocations`. Tuto vlastnost je možné nastavit na seznam názvů oblastí. V rámci vlastnosti `PreferredLocations` je možné zadat zobrazované názvy [oblastí Azure][regions].

> [!NOTE]
> Adresy URL koncových bodů by se neměly považovat za dlouhodobé konstanty. Služba je může kdykoli aktualizovat. Sada SDK tuto změnu zpracuje automaticky.
>
>

```cs
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to Azure Cosmos DB
await docClient.OpenAsync().ConfigureAwait(false);
```

To je vše, tento kurz je u konce. Informace o správě konzistence vašeho globálně replikovaného účtu najdete v tématu [Úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md). Další informace o fungování globální replikace databází ve službě Azure Cosmos DB najdete v tématu [Globální distribuce dat pomocí služby Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí rozhraní SQL API

Teď můžete přejít k dalšímu kurzu, ve kterém se naučíte vyvíjet místně s využitím místního emulátoru služby Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Místní vývoj s využitím emulátoru](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

