---
title: 'Azure Cosmos DB: hromadný prováděcí modul .NET API, sada SDK & prostředků'
description: Seznamte se se všemi o rozhraních .NET API hromadného prováděcího modulu a sadě SDK včetně dat vydání, dat o vyřazení a změn provedených mezi jednotlivými verzemi Azure Cosmos DB hromadných prováděcích sad .NET SDK.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/08/2019
ms.author: ramkris
ms.openlocfilehash: 90537a7e24e5f87087fb718322a7271043d1d229
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176833"
---
# <a name="net-bulk-executor-library-download-information"></a>Knihovna hromadného prováděcího modulu .NET: informace o stažení 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Kanál změn .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Asynchronní Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný prováděcí modul – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný prováděcí modul – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Popis**| Knihovna hromadného prováděcího modulu .NET umožňuje klientským aplikacím provádět hromadné operace s účty Azure Cosmos DB. Tato knihovna poskytuje obory názvů BulkImport, BulkUpdate a BulkDelete. Modul BulkImport dokáže hromadně ingestovat dokumenty optimalizovaným způsobem tak, že propustnost zřízená pro kolekci se spotřebovává do svého maximálního rozsahu. Modul BulkUpdate může hromadně aktualizovat existující data v kontejnerech Azure Cosmos jako opravy. Modul BulkDelete může rychle odstranit dokumenty optimalizovaným způsobem tak, aby propustnost zřízená pro kolekci byla spotřebována do maximálního rozsahu.|
|**Stažení sady SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Knihovna hromadných prováděcích modulů na GitHubu**| [GitHubu](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace rozhraní .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Začínáme**|[Začínáme s knihovnou .NET SDK pro hromadnou vykonavatelskou práci](bulk-executor-dot-net.md)|
| **Aktuální podporovaná architektura**| Microsoft .NET Framework 4.5.2, 4.6.1 a .NET Standard 2,0 |

## <a name="release-notes"></a>Zpráva k vydání verze

### <a name="a-name240-preview240-preview"></a>@no__t – 02.4.0 – Preview

* Změna závislosti sady SDK na > = 2.5.1

### <a name="a-name230-preview2230-preview2"></a>@no__t – 02.3.0 – preview2

* Přidání podpory hromadného prováděcího modulu grafu pro příjem hodnoty TTL u vrcholů a hran

### <a name="a-name220-preview2220-preview2"></a>@no__t – 02.2.0 – preview2

* Opravili jsme problém, což způsobilo výjimky během elastického škálování Azure Cosmos DB při spuštění v režimu brány. Tato oprava je funkčně ekvivalentní verzi 1.4.1.

### <a name="a-name210-preview2210-preview2"></a>@no__t – 02.1.0 – preview2

* Přidali jsme podporu BulkDelete pro účty rozhraní SQL API k přijetí klíče oddílu, k odstranění řazených kolekcí členů dokumentu. Tato změna zajišťuje funkčně rovnocennou verzi 1.4.0.

### <a name="a-name200-preview2200-preview2"></a>@no__t – 02.0.0 – preview2

* Včetně MongoBulkExecutor pro podporu .NET Standard 2,0. Tato funkce je funkčně ekvivalentní verzi 1.3.0, s přidáním podpory .NET Standard 2,0 jako cílové rozhraní.

### <a name="a-name200-preview200-preview"></a>@no__t – 02.0.0 – Preview

* Přidání .NET Standard 2,0 jako jedné z podporovaných cílových rozhraní umožňuje, aby knihovna hromadného prováděcího modulu fungovala s aplikacemi .NET Core.

### <a name="a-name182182"></a>@no__t – 01.8.2

* Pevná vysoká spotřeba procesoru v některých scénářích.
* Trasování teď používá TraceSource. Uživatelé můžou definovat naslouchací procesy pro zdroj `BulkExecutorTrace`.
* Opravili jsme vzácnou situaci, která by mohla způsobit zámek při posílání dokumentů s velikostí 2 MB.

### <a name="a-name160160"></a>@no__t – 01.6.0

* Byl aktualizován hromadný prováděcí modul, aby nyní používal nejnovější verzi sady Azure Cosmos DB .NET SDK (2.4.0).

### <a name="a-name150150"></a>@no__t – 01.5.0

* Přidání podpory hromadného prováděcího modulu grafu pro příjem hodnoty TTL u vrcholů a hran

### <a name="a-name141141"></a>@no__t – 01.4.1

* Opravili jsme problém, což způsobilo výjimky během elastického škálování Azure Cosmos DB při spuštění v režimu brány.

### <a name="a-name140140"></a>@no__t – 01.4.0

* Přidali jsme podporu BulkDelete pro účty rozhraní SQL API k přijetí klíče oddílu, k odstranění řazených kolekcí členů dokumentu.

### <a name="a-name130130"></a>@no__t – 01.3.0

* Opravili jsme problém, což způsobilo problém s formátováním uživatelského agenta používaného hromadným vykonavatelem.

### <a name="a-name120120"></a>@no__t – 01.2.0

* Nastavilo se zlepšení hromadného prováděcího modulu pro import a aktualizaci rozhraní API pro účely transparentního přizpůsobování elastického škálování Cosmos kontejneru, když úložiště překročí aktuální kapacitu bez vyvolání výjimek.

### <a name="a-name112112"></a>@no__t – 01.1.2

* DocumentDB závislost sady .NET SDK na verzi 2.1.3.

### <a name="a-name111111"></a>@no__t – 01.1.1

* Opravili jsme problém, což způsobilo, že hromadný vykonavatel při importu do pevných kolekcí vyvolal chybu JSRT.

### <a name="a-name110110"></a>@no__t – 01.1.0

* Přidání podpory pro operaci BulkDelete pro účty Azure Cosmos DB rozhraní SQL API
* Přidala se podpora pro BulkImport operace pro účty s rozhraním API Azure Cosmos DB pro MongoDB.
* DocumentDB závislost sady .NET SDK na verzi 2.0.0. 

### <a name="a-name102102"></a>@no__t – 01.0.2

* Přidání podpory pro operaci BulkImport pro účty rozhraní API pro Azure Cosmos DB Gremlin

### <a name="a-name101101"></a>@no__t – 01.0.1

* Menší Oprava chyby BulkImport operace pro účty rozhraní SQL API Azure Cosmos DB.

### <a name="a-name100100"></a>@no__t – 01.0.0

* Přidání podpory pro operace BulkImport a BulkUpdate pro Azure Cosmos DB účty rozhraní SQL API

## <a name="next-steps"></a>Další kroky

Další informace o knihovně Java pro hromadné prováděcí moduly najdete v následujícím článku:

[Sada SDK hromadných prováděcích modulů Java a informace o verzi](sql-api-sdk-bulk-executor-java.md)
