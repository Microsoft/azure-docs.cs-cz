---
title: Indexery pro procházení zdrojů dat během indexování – Azure Search
description: Procházejte databázi Azure SQL, službu Azure Cosmos DB nebo úložiště Azure, extrahujte prohledávatelná data a naplňte jimi index služby Azure Search.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: e50d88181a27dcc46da858f220404eb09ad9b4bd
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308970"
---
# <a name="indexers-in-azure-search"></a>Indexery ve službě Azure Search

*Indexer* v Azure Search je prohledávací modul, který extrahuje hledaná data a metadata z externího zdroje dat Azure a naplní index založený na mapování polí mezi indexem a zdrojem dat. Tento přístup se někdy označuje jako "pull model", protože služba získává data v, aniž byste museli psát kód, který do indexu přidává data.

Indexery jsou založené na typech nebo platformách zdrojů dat a jednotlivé indexery pro SQL Server v Azure, Cosmos DB, Azure Table Storage a Blob Storage. Indexery úložiště objektů BLOB mají další vlastnosti specifické pro typy obsahu objektů BLOB.

Můžete použít indexer jako jediný prostředek přijímání dat, nebo můžete použít kombinaci postupů, kdy se indexer použije k načtení jenom některých polí v indexu.

Indexery můžete spouštět na vyžádání nebo podle plánu opakované aktualizace dat, který se spouští tak často, jak každých pět minut. Častější aktualizace vyžadují model Push, který aktualizuje data současně ve službě Azure Search i v externím zdroji dat.

## <a name="approaches-for-creating-and-managing-indexers"></a>Přístupy k vytváření a správě indexerů

Indexery můžete vytvářet a spravovat pomocí těchto přístupů:

* [Průvodce importem dat > portálu](search-import-data-portal.md)
* [Rozhraní API služby REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Nový indexer je zpočátku oznámen jako funkce ve verzi Preview. Funkce ve verzi Preview se zavádějí v rozhraních API (REST a .NET) a po přechodu do všeobecné dostupnosti se integrují do portálu. Pokud vyhodnocujete nový indexer, měli byste počítat s psaním kódu.

## <a name="permissions"></a>Oprávnění

Všechny operace související s indexery, včetně požadavků GET pro stav nebo definice, vyžadují [klíč rozhraní API pro správu](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Podporované zdroje dat

Úložiště dat procházení indexerů v Azure

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)

## <a name="basic-configuration-steps"></a>Postup základní konfigurace
Indexery můžou nabízet funkce, které jsou jedinečné pro daný zdroj dat. Z toho důvodu se budou některé aspekty konfigurace indexeru nebo zdroje dat lišit podle typu indexeru. Všechny indexery ale sdílejí stejné základní složení a požadavky. Níže najdete popis kroků společných pro všechny indexery.

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat
Indexer získá připojení ke zdroji dat z objektu *zdroje dat* . Definice zdroje dat poskytuje připojovací řetězec a případně přihlašovací údaje. Chcete-li vytvořit prostředek, zavolejte třídu [Create datasource](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API nebo [DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) .

Zdroje dat se konfigurují a spravují nezávisle na indexerech, které je používají, což znamená, že několik indexerů může používat zdroj dat k načtení více indexů současně.

### <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu
Indexer automatizuje některé úkoly související s příjmem dat, ale vytváření indexu k nim obvykle nepatří. K základním požadavkům patří předdefinovaný index s poli, která odpovídají polím v externím zdroji dat. Pole musí odpovídat názvu a datovému typu. Další informace o strukturování indexu naleznete v tématu [Create a index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) nebo [třída indexu](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Nápovědu k přidružení polí najdete v tématu [Mapování polí v indexerech Azure Search](search-indexer-field-mappings.md).

> [!Tip]
> Přestože indexery nedokážou vygenerovat index za vás, může vám pomoct průvodce **importem dat** na portálu. Ve většině případů dokáže průvodce odvodit schéma indexu ze stávajících metadat ve zdroji a zobrazit předběžné schéma indexu, které můžete upravit přímo v aktivním průvodci. Po vytvoření indexu ve službě jsou další úpravy na portálu omezené hlavně na přidávání nových polí. K vytvoření, ale ne revidování, indexu zvažte použití průvodce. Praktickou výuku najdete v [průvodci portálem](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Krok 3: Vytvoření a naplánování indexeru
Definice indexeru je konstrukce, která spojuje všechny prvky související s přijímáním dat. Mezi požadované prvky patří zdroj dat a index. Mezi volitelné prvky patří mapování plánu a polí. Mapování polí je nepovinné pouze v případě, že zdrojová pole a indexová pole jasně odpovídají. Indexer můžete odkazovat na zdroj dat z jiné služby, pokud se tento zdroj dat nachází ve stejném předplatném. Další informace o strukturování indexeru najdete v tématu [Vytvoření indexeru (rozhraní API Azure Search REST)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Spustit indexery na vyžádání

I když je běžné indexování běžné, lze indexer vyvolat také na vyžádání pomocí [příkazu Run](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Když se rozhraní API spustí úspěšně, vyvolání indexeru se naplánuje, ale vlastní zpracování proběhne asynchronně. 

Stav indexeru můžete monitorovat na portálu nebo prostřednictvím rozhraní API pro získání stavu indexeru. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Získat stav indexeru

Můžete načíst stav a historii spouštění indexeru pomocí [příkazu Get indexer status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Odpověď obsahuje celkový stav indexeru, vyvolání posledního (nebo probíhajícího) indexeru a historii posledních vyvolání indexeru.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Historie spouštění obsahuje až 50 posledních dokončených spuštění, která jsou seřazena v opačném chronologickém pořadí (takže poslední spuštění nastane jako první v odpovědi).

## <a name="next-steps"></a>Další postup
Teď jste získali základní představu. V dalším kroku se zaměříme na požadavky a úlohy specifické pro různé typy zdrojů dat.

* [Azure SQL Database nebo SQL Server na virtuálním počítači Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Indexování objektů blob CSV pomocí indexeru Azure Search Blob](search-howto-index-csv-blobs.md)
* [Indexování objektů blob JSON pomocí indexeru Azure Search Blob](search-howto-index-json-blobs.md)
