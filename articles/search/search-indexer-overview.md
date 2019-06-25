---
title: Indexery pro procházení zdroje dat během indexování – vyhledávání Azure
description: Procházejte databázi Azure SQL, službu Azure Cosmos DB nebo úložiště Azure, extrahujte prohledávatelná data a naplňte jimi index služby Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6871c75f0af662762ffac6e7346d4999e55eb937
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303443"
---
# <a name="indexers-in-azure-search"></a>Indexery ve službě Azure Search

*Indexer* ve službě Azure Search je prohledávací modul, který extrahuje prohledávatelná data a metadata z externího Azure zdroje dat a naplní index na základě mapování pole pole mezi indexem a zdroje dat. Tento přístup je někdy označovány jako "model na vyžádání", protože služba si vyžádá data, aniž byste museli psát jakýkoli kód, který přidá data do indexu.

Indexery jsou založené na data typech nebo platformách zdrojů, existují samostatné indexery pro SQL Server na Azure, služby Cosmos DB, Azure Table Storage a Blob Storage. Indexování objektů BLOB storage mají další vlastnosti specifické pro typy obsahu objektu blob.

Můžete použít indexer jako jediný prostředek přijímání dat, nebo můžete použít kombinaci postupů, kdy se indexer použije k načtení jenom některých polí v indexu.

Můžete spustit indexery na vyžádání nebo dat plánu aktualizace, která spouští tak často, jak každých pět minut. Častější aktualizace vyžadují model Push, který aktualizuje data současně ve službě Azure Search i v externím zdroji dat.

## <a name="approaches-for-creating-and-managing-indexers"></a>Přístupy k vytváření a správě indexerů

Indexery můžete vytvářet a spravovat pomocí těchto přístupů:

* [Portál > Průvodce importem dat](search-import-data-portal.md)
* [Rozhraní API služby REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Nový indexer je zpočátku oznámen jako funkce ve verzi Preview. Funkce ve verzi Preview se zavádějí v rozhraních API (REST a .NET) a po přechodu do všeobecné dostupnosti se integrují do portálu. Pokud vyhodnocujete nový indexer, měli byste počítat s psaním kódu.

## <a name="permissions"></a>Oprávnění

Všechny operace související s indexery, včetně požadavků GET pro stav nebo definice, vyžadují [správce rozhraní api-key](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Podporované zdroje dat

Indexery procházet úložiště dat v Azure.

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md) 

> [!Note]
> Azure Table Storage se nepodporuje pro [kognitivního vyhledávání](cognitive-search-concept-intro.md).
>

## <a name="basic-configuration-steps"></a>Postup základní konfigurace
Indexery můžou nabízet funkce, které jsou jedinečné pro daný zdroj dat. Z toho důvodu se budou některé aspekty konfigurace indexeru nebo zdroje dat lišit podle typu indexeru. Všechny indexery ale sdílejí stejné základní složení a požadavky. Níže najdete popis kroků společných pro všechny indexery.

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat
Indexer získává připojení ke zdroji dat z *zdroj dat* objektu. Definice zdroje dat obsahuje připojovací řetězec a případně i přihlašovací údaje. Volání [vytvořit zdroj dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) rozhraní REST API nebo [třídy DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) a vytvoří prostředek.

Zdroje dat se konfigurují a spravují nezávisle na indexerech, které je používají, což znamená, že několik indexerů může používat zdroj dat k načtení více indexů současně.

### <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu
Indexer automatizuje některé úkoly související s příjmem dat, ale vytváření indexu k nim obvykle nepatří. K základním požadavkům patří předdefinovaný index s poli, která odpovídají polím v externím zdroji dat. Pole musí odpovídat podle názvu a datového typu. Další informace o strukturování indexu najdete v tématu [vytvoření indexu (REST Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) nebo [Index – třída](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Nápovědu k přidružení polí najdete v tématu [Mapování polí v indexerech Azure Search](search-indexer-field-mappings.md).

> [!Tip]
> Přestože indexery nedokážou vygenerovat index za vás, může vám pomoct průvodce **importem dat** na portálu. Ve většině případů dokáže průvodce odvodit schéma indexu ze stávajících metadat ve zdroji a zobrazit předběžné schéma indexu, které můžete upravit přímo v aktivním průvodci. Po vytvoření indexu ve službě jsou další úpravy na portálu omezené hlavně na přidávání nových polí. K vytvoření, ale ne revidování, indexu zvažte použití průvodce. Praktickou výuku najdete v [průvodci portálem](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Krok 3: Vytvoření a naplánování indexeru
Definice indexeru je konstrukce, která spojuje všechny elementy související s příjmem dat. Požadované prvky patří zdroj dat a indexu. Volitelné prvky patří mapování plánu a pole. Mapování pole jsou pouze volitelné, pokud jasně odpovídají zdrojové pole a indexu pole. Indexer můžete odkazovat na zdroj dat z jiné služby, pokud se tento zdroj dat nachází ve stejném předplatném. Další informace o strukturování indexeru najdete v tématu [Vytvoření indexeru (rozhraní API Azure Search REST)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Spuštění indexery na vyžádání

I když je společné pro naplánovat indexování, indexer může být vyvoláno také ručně pomocí [spusťte příkaz](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Při spuštění rozhraní API vrátí úspěšně, vyvolání indexeru je naplánovaná, ale vlastní zpracování probíhá asynchronně. 

Můžete monitorovat stav indexeru na portálu nebo prostřednictvím rozhraní API stavu získat indexeru. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Získat stav indexeru

Můžete načíst historii stavu a spuštění indexeru prostřednictvím [příkazu získat stav Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Odpověď obsahuje celkový stav indexer, indexer poslední (nebo probíhající) volání a historii posledních vyvolání indexeru.

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

Historie provádění obsahuje až 50 poslední dokončené spuštění, které jsou řazeny zpětného chronologicky (aby poslední spuštění je dodávána první v odpovědi).

## <a name="next-steps"></a>Další postup
Teď jste získali základní představu. V dalším kroku se zaměříme na požadavky a úlohy specifické pro různé typy zdrojů dat.

* [Azure SQL Database nebo SQL Server na virtuálním počítači Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Indexování objektů blob CSV pomocí indexeru Azure Search Blob](search-howto-index-csv-blobs.md)
* [Indexování objektů blob JSON pomocí indexeru Azure Search Blob](search-howto-index-json-blobs.md)
