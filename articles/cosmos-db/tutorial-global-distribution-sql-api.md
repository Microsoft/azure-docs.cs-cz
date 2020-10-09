---
title: 'Kurz: Azure Cosmos DB globální distribuční kurz pro rozhraní SQL API'
description: 'Kurz: Naučte se, jak nastavit globální distribuci Azure Cosmos DB pomocí rozhraní SQL API s využitím .NET, Java, Pythonu a různých dalších sad SDK.'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: ebb6bdf6e5072d1024ba7abcd1db1cf7bebfcd3d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334374"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Kurz: nastavení globální distribuce Azure Cosmos DB pomocí rozhraní SQL API

V tomto článku se dozvíte, jak pomocí Azure Portal nastavit globální distribuci Azure Cosmos DB a pak se připojit pomocí rozhraní SQL API.

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí [rozhraní SQL API](sql-api-introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a><a id="preferred-locations"></a> Připojení k preferované oblasti pomocí rozhraní SQL API

Aby mohly využívat [globální distribuci](distribute-data-globally.md), můžou mít klientské aplikace určený seřazený seznam preferovaných oblastí, které se mají použít k provádění operací s dokumenty. V závislosti na konfiguraci účtu služby Azure Cosmos DB, aktuální regionální dostupnosti a zadaného seznamu preferencí zvolí sada SQL SDK optimální koncový bod, který bude provádět operace čtení a zápisu.

Tento seznam preferencí se zadává při inicializaci připojení pomocí sad SQL SDK. Sady SDK přijímají volitelný parametr `PreferredLocations` , který je uspořádaný seznam oblastí Azure.

Sada SDK bude automaticky odesílat všechny operace zápisu do aktuální oblasti pro zápis. Všechna čtení budou odeslána do první dostupné oblasti v seznamu upřednostňovaná umístění. Pokud požadavek selže, klient neprojde seznam do další oblasti.

Sada SDK se bude pokoušet pouze číst z oblastí určených v upřednostňovaných umístěních. Pokud je například účet Azure Cosmos k dispozici ve čtyřech oblastech, ale klient v rámci nástroje určuje pouze dvě oblasti čtení (bez zápisu), `PreferredLocations` pak žádné čtení nebudou obsluhovány z oblasti čtení, která není určena v `PreferredLocations` . Pokud oblasti čtení zadané v seznamu nejsou `PreferredLocations` k dispozici, budou načteny mimo oblast zápisu.

Aplikace může ověřit aktuální koncový bod zápisu a číst koncový bod vybraný sadou SDK, a to kontrolou dvou vlastností `WriteEndpoint` a `ReadEndpoint` dostupných v sadě SDK verze 1,8 a vyšší. Pokud `PreferredLocations` vlastnost není nastavena, všechny požadavky budou obsluhovány z aktuální oblasti pro zápis.

Pokud nezadáte upřednostňovaná umístění, ale použili jste `setCurrentLocation` metodu, sada SDK automaticky naplní upřednostňovaná umístění na základě aktuální oblasti, ve které je spuštěný klient nástroje. Sada SDK řadí oblasti na základě blízkosti oblasti v aktuální oblasti.

## <a name="net-sdk"></a>.NET SDK

Sadu SDK můžete využívat bez jakýchkoli změn kódu. V tomto případě sada SDK automaticky směruje operace čtení i zápisu do aktuální oblasti pro zápis.

V sadě .NET SDK verze 1.8 a novější má parametr ConnectionPolicy pro konstruktor DocumentClient vlastnost Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Tato vlastnost je typu Kolekce `<string>` a měla by obsahovat seznam názvů oblastí. Řetězcové hodnoty jsou formátovány podle sloupce název oblasti na stránce [oblasti Azure][regions] , bez mezer před nebo za prvním a posledním znakem.

Aktuální koncové body pro čtení a zápis jsou k dispozici ve vlastnostech DocumentClient.WriteEndpoint a DocumentClient.ReadEndpoint.

> [!NOTE]
> Adresy URL koncových bodů by se neměly považovat za dlouhodobé konstanty. Služba je může kdykoli aktualizovat. Sada SDK tuto změnu zpracuje automaticky.
>

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

Pokud používáte sadu .NET v2 SDK, použijte `PreferredLocations` vlastnost k nastavení upřednostňované oblasti.

```csharp
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

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

Alternativně můžete použít `SetCurrentLocation` vlastnost a nechat sadu SDK zvolit preferované umístění na základě blízkosti.

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

connectionPolicy.SetCurrentLocation("West US 2"); /

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Pokud používáte sadu .NET V3 SDK, použijte `ApplicationPreferredRegions` vlastnost k nastavení upřednostňované oblasti.

```csharp

CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
options.ApplicationPreferredRegions = new List<string> {Regions.WestUS, Regions.WestUS2};

CosmosClient client = new CosmosClient(connectionString, options);

```

Alternativně můžete použít `ApplicationRegion` vlastnost a nechat sadu SDK zvolit preferované umístění na základě blízkosti.

```csharp
CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
// If the application is running in West US
options.ApplicationRegion = Regions.WestUS;

CosmosClient client = new CosmosClient(connectionString, options);
```

---

## <a name="nodejsjavascript"></a>Node.js/JavaScript

> [!NOTE]
> Adresy URL koncových bodů by se neměly považovat za dlouhodobé konstanty. Služba je může kdykoli aktualizovat. Sada SDK tuto změnu zpracuje automaticky.
>
>

Níže je příklad kódu pro Node.js/JavaScript.

```JavaScript
// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
const preferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
const client = new CosmosClient{ endpoint, key, connectionPolicy: { preferredLocations } });
```

## <a name="python-sdk"></a>Python SDK

Následující kód ukazuje, jak nastavit Upřednostňovaná umístění pomocí sady Python SDK:

```python
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v4-sdk"></a><a id="java4-preferred-locations"></a> Sada Java v4 SDK

Následující kód ukazuje, jak nastavit Upřednostňovaná umístění pomocí sady Java SDK:

# <a name="async"></a>[Async](#tab/api-async)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TutorialGlobalDistributionPreferredLocationAsync)]

# <a name="sync"></a>[Synchronizovat](#tab/api-sync)

   [Sada Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=TutorialGlobalDistributionPreferredLocationSync)]

--- 

## <a name="rest"></a>REST

Po zpřístupnění účtu databáze ve více oblastech můžou klienti zadat dotaz na jeho dostupnost provedením žádosti o získání tohoto identifikátoru URI. `https://{databaseaccount}.documents.azure.com/`

Služba vrátí seznam oblastí a jejich odpovídajících identifikátorů URI koncových bodů služby Azure Cosmos DB pro repliky. V odpovědi bude uvedená aktuální oblast pro zápis. Klient si pak může vybrat vhodný koncový bod pro všechny další požadavky rozhraní REST API následujícím způsobem.

Příklad odpovědi

```json
{
    "_dbs": "//dbs/",
    "media": "//media/",
    "writableLocations": [
        {
            "Name": "West US",
            "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
        }
    ],
    "readableLocations": [
        {
            "Name": "East US",
            "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
        }
    ],
    "MaxMediaStorageUsageInMB": 2048,
    "MediaStorageUsageInMB": 0,
    "ConsistencyPolicy": {
        "defaultConsistencyLevel": "Session",
        "maxStalenessPrefix": 100,
        "maxIntervalInSeconds": 5
    },
    "addresses": "//addresses/",
    "id": "globaldbexample",
    "_rid": "globaldbexample.documents.azure.com",
    "_self": "",
    "_ts": 0,
    "_etag": null
}
```

* Všechny požadavky PUT,POST a DELETE musí směřovat na uvedený identifikátor URI pro zápis.
* Všechny požadavky GET a další žádosti jen pro čtení (například dotazy) můžou přejít na libovolný koncový bod ve výběru klienta.

Požadavky na zápis do oblastí jen pro čtení nebudou úspěšné. Zobrazí se kód chyby HTTP 403 (zakázáno).

Pokud se po prvotní fázi zjišťování klienta změní oblast zápisu, následné zápisy do předchozí oblasti zápisu selžou s kódem chyby HTTP 403 ("zakázáno"). Klient pak musí pomocí požadavku GET znovu získat seznam oblastí, aby získal aktualizovanou oblast pro zápis.

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

