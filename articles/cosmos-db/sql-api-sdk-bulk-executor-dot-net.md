---
title: 'Azure Cosmos DB: hromadný prováděcí modul .NET API, sada SDK & prostředků'
description: Seznamte se se všemi o rozhraních .NET API hromadného prováděcího modulu a sadě SDK včetně dat vydání, dat o vyřazení a změn provedených mezi jednotlivými verzemi Azure Cosmos DB hromadných prováděcích sad .NET SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: 5203223f8468a4dfa0faf2df92e826a62f7bbfcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802748"
---
# <a name="net-bulk-executor-library-download-information"></a>Knihovna hromadného prováděcího modulu .NET: informace o stažení 

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [Sada .NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [Rozhraní .NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Sada Java SDK v4](sql-api-sdk-java-v4.md)
> * [Sada Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sada Sync Java SDK v2](sql-api-sdk-java.md)
> * [Jarní data v2](sql-api-sdk-java-spring-v2.md)
> * [Jarní data V3](sql-api-sdk-java-spring-v3.md)
> * [Konektor Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný prováděcí modul – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný prováděcí modul – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Popis**| Knihovna hromadného prováděcího modulu .NET umožňuje klientským aplikacím provádět hromadné operace s účty Azure Cosmos DB. Tato knihovna poskytuje obory názvů BulkImport, BulkUpdate a BulkDelete. Modul BulkImport dokáže hromadně ingestovat dokumenty optimalizovaným způsobem tak, že propustnost zřízená pro kolekci se spotřebovává do svého maximálního rozsahu. Modul BulkUpdate může hromadně aktualizovat existující data v kontejnerech Azure Cosmos jako opravy. Modul BulkDelete může rychle odstranit dokumenty optimalizovaným způsobem tak, aby propustnost zřízená pro kolekci byla spotřebována do maximálního rozsahu.|
|**Stažení sady SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Knihovna hromadných prováděcích modulů na GitHubu**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace rozhraní .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet&preserve-view=true)|
|**Začínáme**|[Začínáme s knihovnou .NET SDK pro hromadnou vykonavatelskou práci](bulk-executor-dot-net.md)|
| **Aktuální podporovaná architektura**| Microsoft .NET Framework 4.5.2, 4.6.1 a .NET Standard 2,0 |

> [!NOTE]
> Pokud používáte hromadný prováděcí modul, přečtěte si prosím nejnovější verzi 3. x sady [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md), která obsahuje hromadný prováděcí modul integrovaný do sady SDK. 

## <a name="release-notes"></a>Poznámky k verzi

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1 – Preview

* Opravili TotalElapsedTime v reakci na BulkDelete pro správné měření celkového času včetně všech opakovaných pokusů.

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0 – Preview

* Změna závislosti sady SDK na >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0-preview2

* Přidání podpory hromadného prováděcího modulu grafu pro příjem hodnoty TTL u vrcholů a hran

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0 – preview2

* Opravili jsme problém, což způsobilo výjimky během elastického škálování Azure Cosmos DB při spuštění v režimu brány. Tato oprava je funkčně ekvivalentní verzi 1.4.1.

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0 – preview2

* Přidali jsme podporu BulkDelete pro účty rozhraní SQL API k přijetí klíče oddílu, k odstranění řazených kolekcí členů dokumentu. Tato změna zajišťuje funkčně rovnocennou verzi 1.4.0.

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0 – preview2

* Včetně MongoBulkExecutor pro podporu .NET Standard 2,0. Tato funkce je funkčně ekvivalentní verzi 1.3.0, s přidáním podpory .NET Standard 2,0 jako cílové rozhraní.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0 – Preview

* Přidání .NET Standard 2,0 jako jedné z podporovaných cílových rozhraní umožňuje, aby knihovna hromadného prováděcího modulu fungovala s aplikacemi .NET Core.

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* Opravili jsme problém s BulkDeleteAsync, když byly hodnoty s řídicími uvozovkami předány jako vstupní parametry.

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* Opravili jsme problém na MongoBulkExecutor, který neočekávaně zvýšil velikost dokumentu přidáním odsazení a v některých případech, při překročení limitu maximální velikosti dokumentu.

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* Opravili jsme problém s BulkDeleteAsync, když kolekce obsahuje cesty k klíčům vnořeného oddílu.

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* MongoBulkExecutor nyní implementuje rozhraní IDisposable a očekává se, že bude po použití uvolněn.

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* Zámek na verzi sady SDK byl odebrán. Balíček je teď závislý na SDK >= 2.5.1.

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* Pevné zpracování identifikátorů při volání BulkImport se seznamem objektů POCO s číselnými hodnotami.

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* Opravili TotalElapsedTime v reakci na BulkDelete pro správné měření celkového času včetně všech opakovaných pokusů.

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* Pevná vysoká spotřeba procesoru v některých scénářích.
* Trasování teď používá TraceSource. Uživatelé můžou definovat naslouchací procesy pro `BulkExecutorTrace` zdroj.
* Opravili jsme vzácnou situaci, která by mohla způsobit zámek při posílání dokumentů s velikostí 2 MB.

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* Byl aktualizován hromadný prováděcí modul, aby nyní používal nejnovější verzi sady Azure Cosmos DB .NET SDK (2.4.0).

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* Přidání podpory hromadného prováděcího modulu grafu pro příjem hodnoty TTL u vrcholů a hran

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* Opravili jsme problém, což způsobilo výjimky během elastického škálování Azure Cosmos DB při spuštění v režimu brány.

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* Přidali jsme podporu BulkDelete pro účty rozhraní SQL API k přijetí klíče oddílu, k odstranění řazených kolekcí členů dokumentu.

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* Opravili jsme problém, což způsobilo problém s formátováním uživatelského agenta používaného hromadným vykonavatelem.

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* Nastavilo se zlepšení hromadného prováděcího modulu pro import a aktualizaci rozhraní API pro účely transparentního přizpůsobování elastického škálování Cosmos kontejneru, když úložiště překročí aktuální kapacitu bez vyvolání výjimek.

### <a name="112"></a><a name="1.1.2"></a>bodu

* DocumentDB závislost sady .NET SDK na verzi 2.1.3.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Opravili jsme problém, což způsobilo, že hromadný vykonavatel při importu do pevných kolekcí vyvolal chybu JSRT.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Přidání podpory pro operaci BulkDelete pro účty Azure Cosmos DB rozhraní SQL API
* Přidala se podpora pro BulkImport operace pro účty s rozhraním API Azure Cosmos DB pro MongoDB.
* DocumentDB závislost sady .NET SDK na verzi 2.0.0. 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* Přidání podpory pro operaci BulkImport pro účty rozhraní API pro Azure Cosmos DB Gremlin

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* Menší Oprava chyby BulkImport operace pro účty rozhraní SQL API Azure Cosmos DB.

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Přidání podpory pro operace BulkImport a BulkUpdate pro Azure Cosmos DB účty rozhraní SQL API

## <a name="next-steps"></a>Další kroky

Další informace o knihovně Java pro hromadné prováděcí moduly najdete v následujícím článku:

[Sada SDK hromadných prováděcích modulů Java a informace o verzi](sql-api-sdk-bulk-executor-java.md)
