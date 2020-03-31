---
title: 'Azure Cosmos DB: Hromadné vykonavatel .NET API, sada SDK & prostředky'
description: Přečtěte si vše o hromadném prováděcím modulu .NET API a SDK, včetně dat vydání, dat odchodu do důchodu a změn provedených mezi jednotlivými verzemi hromadného vykonavatele služby Azure Cosmos DB .NET SDK.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169400"
---
# <a name="net-bulk-executor-library-download-information"></a>Knihovna hromadného prováděcího modulu .NET: Stáhnout informace 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Informační kanál o změně .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [Odpočinku](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný vykonavatel - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný vykonavatel - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Popis**| Knihovna hromadného vykonavatele .Net umožňuje klientským aplikacím provádět hromadné operace na účtech Azure Cosmos DB. Tato knihovna poskytuje BulkImport, BulkUpdate a BulkDelete obory názvů. BulkImport modul můžete hromadně ingestování dokumentů optimalizovaným způsobem tak, aby propustnost zřízená pro kolekci je spotřebována v maximální míře. Modul BulkUpdate můžete hromadně aktualizovat existující data v kontejnerech Azure Cosmos jako opravy. BulkDelete modul můžete hromadně odstranit dokumenty optimalizovaným způsobem tak, aby propustnost zřízená pro kolekci je spotřebována v maximální míře.|
|**SDK ke stažení**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Knihovna hromadného prováděcího modulu v GitHubu**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace rozhraní .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Začínáme**|[Začínáme s knihovnou hromadného prováděcího modulu .NET SDK](bulk-executor-dot-net.md)|
| **Aktuální podporovaný rámec**| Rozhraní Microsoft .NET Framework 4.5.2, 4.6.1 a .NET Standard 2.0 |

> [!NOTE]
> Pokud používáte hromadné vykonavatel, naleznete nejnovější verzi 3.x [sady .NET SDK](tutorial-sql-api-dotnet-bulk-import.md), která má hromadné vykonavatel zabudované do sady SDK. 

## <a name="release-notes"></a>Poznámky k verzi

### <a name="241-preview"></a><a name="2.4.1-preview"/>2.4.1-náhled

* Opraven a kontum a příkaz TotalElapsedTime v odpovědi funkce BulkDelete správně změřit celkový čas včetně všech opakování.

### <a name="240-preview"></a><a name="2.4.0-preview"/>2.4.0-náhled

* Závislost sady SDK byla změněna na >= 2,5,1

### <a name="230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-náhled2

* Přidána podpora hromadného vykonavatele grafu pro přijetí ttl na vrcholech a hranách

### <a name="220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-náhled2

* Opraven problém, který při spuštění v režimu brány způsoboval výjimky při elastickém škálování služby Azure Cosmos DB. Díky této opravě je funkčně ekvivalentní uvolnění 1.4.1.

### <a name="210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-náhled2

* Přidána podpora BulkDelete pro účty SQL API pro přijetí klíče oddílu, n-tic id dokumentu k odstranění. Díky této změně je funkčně ekvivalentní verzi 1.4.0.

### <a name="200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-náhled2

* Včetně MongoBulkExecutor pro podporu .NET Standard 2.0. Tato funkce umožňuje funkčně ekvivalentní verzi 1.3.0 s přidáním podpůrného rozhraní .NET Standard 2.0 jako cílového rozhraní.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-náhled

* Jako jeden z podporovaných cílových architektur byl přidán soubor .NET Standard 2.0, aby knihovna hromadného prováděcího modulu fungovala s aplikacemi .NET Core.

### <a name="188"></a><a name="1.8.8"/>1.8.8

* Opraven problém s mongobulkexecutorem, který neočekávaně zvyšoval velikost dokumentu přidáním odsazení a v některých případech překročením maximálního limitu velikosti dokumentu.

### <a name="187"></a><a name="1.8.7"/>1.8.7

* Opraven problém s BulkDeleteAsync, když kolekce má vnořené cesty klíče oddílu.

### <a name="186"></a><a name="1.8.6"/>1.8.6

* MongoBulkExecutor nyní implementuje IDisposable a očekává se, že bude zlikvidován po použití.

### <a name="185"></a><a name="1.8.5"/>1.8.5

* Odebrán zámek ve verzi sady SDK. Balíček je nyní závislá na sdk >= 2.5.1.

### <a name="184"></a><a name="1.8.4"/>1.8.4

* Opraveno zpracování identifikátorů při volání BulkImport se seznamem objektů POCO s číselnými hodnotami.

### <a name="183"></a><a name="1.8.3"/>1.8.3

* Opraven a kontum a příkaz TotalElapsedTime v odpovědi funkce BulkDelete správně změřit celkový čas včetně všech opakování.

### <a name="182"></a><a name="1.8.2"/>1.8.2

* Opravena vysoká spotřeba procesoru v určitých scénářích.
* Trasování nyní používá TraceSource. Uživatelé mohou definovat `BulkExecutorTrace` posluchače pro zdroj.
* Opraven vzácný scénář, který mohl způsobit zámek při odesílání dokumentů v blízkosti velikosti 2Mb.

### <a name="160"></a><a name="1.6.0"/>1.6.0

* Byl aktualizován hromadný vykonavatel, aby teď používal nejnovější verzi sady Azure Cosmos DB .NET SDK (2.4.0).

### <a name="150"></a><a name="1.5.0"/>1.5.0

* Přidána podpora hromadného vykonavatele grafu pro přijetí ttl na vrcholech a hranách

### <a name="141"></a><a name="1.4.1"/>1.4.1

* Opraven problém, který při spuštění v režimu brány způsoboval výjimky při elastickém škálování služby Azure Cosmos DB.

### <a name="140"></a><a name="1.4.0"/>1.4.0

* Přidána podpora BulkDelete pro účty SQL API pro přijetí klíče oddílu, n-tic id dokumentu k odstranění.

### <a name="130"></a><a name="1.3.0"/>1.3.0

* Byl opraven problém, který způsoboval problém s formátováním v uživatelském agentovi používaném hromadným vykonavatelem.

### <a name="120"></a><a name="1.2.0"/>1.2.0

* Bylo vylepšení hromadného importu vykonavatele a aktualizace api, aby se transparentně přizpůsobily elastickým škálování kontejneru Cosmos, když úložiště překročí aktuální kapacitu bez vyvolání výjimek.

### <a name="112"></a><a name="1.1.2"/>1.1.2

* Nabuzený do documentdb .NET SDK závislost na verzi 2.1.3.

### <a name="111"></a><a name="1.1.1"/>1.1.1

* Byl opraven problém, který způsoboval hromadné vykonavatele při importu do pevných kolekcí chybou JSRT.

### <a name="110"></a><a name="1.1.0"/>1.1.0

* Byla přidána podpora pro operaci BulkDelete pro účty AZURE Cosmos DB SQL API.
* Přidána podpora pro operaci BulkImport pro účty s rozhraním API Azure Cosmos DB pro MongoDB.
* Navýšil závislost sady DocumentDB .NET SDK na verzi 2.0.0. 

### <a name="102"></a><a name="1.0.2"/>1.0.2

* Přidána podpora pro operaci BulkImport pro účty rozhraní API Azure Cosmos DB Gremlin.

### <a name="101"></a><a name="1.0.1"/>1.0.1

* Menší oprava chyby operace BulkImport pro účty SQL API Azure Cosmos DB.

### <a name="100"></a><a name="1.0.0"/>1.0.0

* Byla přidána podpora pro operace BulkImport a BulkUpdate pro účty SQL API Azure Cosmos DB.

## <a name="next-steps"></a>Další kroky

Další informace o knihovně Java hromadného vykonavatele vyhledání naleznete v následujícím článku:

[Java hromadná vykonavatel knihovna SDK a informace o uvolnění](sql-api-sdk-bulk-executor-java.md)
