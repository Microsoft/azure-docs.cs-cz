---
title: Indexery pro procházení dat během importu
titleSuffix: Azure Cognitive Search
description: Procházení Azure SQL Database, spravované instance SQL, Azure Cosmos DB nebo Azure Storage k extrakci prohledávatelných dat a naplnění indexu služby Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 7f6be959bf09cbe20bb37dfa3d17d64467758bd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397891"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexery ve službě Azure Cognitive Search

*Indexer* v Azure kognitivní hledání je prohledávací modul, který extrahuje hledaná data a metadata z externího zdroje dat Azure a naplní index založený na mapování polí mezi indexem a zdrojem dat. Tento přístup se někdy označuje jako "pull model", protože služba získává data v, aniž byste museli psát kód, který do indexu přidává data.

Indexery jsou založené na typech nebo platformách zdrojů dat a jednotlivé indexery pro SQL Server v Azure, Cosmos DB, Azure Table Storage a Blob Storage. Indexery úložiště objektů BLOB mají další vlastnosti specifické pro typy obsahu objektů BLOB.

Můžete použít indexer jako jediný prostředek přijímání dat, nebo můžete použít kombinaci postupů, kdy se indexer použije k načtení jenom některých polí v indexu.

Indexery můžete spouštět na vyžádání nebo podle plánu opakované aktualizace dat, který se spouští tak často, jak každých pět minut. Častější aktualizace vyžadují model nabízených oznámení, který současně aktualizuje data v Kognitivní hledání Azure i v externím zdroji dat.

## <a name="approaches-for-creating-and-managing-indexers"></a>Přístupy k vytváření a správě indexerů

Indexery můžete vytvářet a spravovat pomocí těchto přístupů:

* [Průvodce importem dat > portálu](search-import-data-portal.md)
* [Rozhraní API služby REST](/rest/api/searchservice/Indexer-operations)
* [.NET SDK](/dotnet/api/microsoft.azure.search.iindexersoperations)

Nový indexer je zpočátku oznámen jako funkce ve verzi Preview. Funkce ve verzi Preview se zavádějí v rozhraních API (REST a .NET) a po přechodu do všeobecné dostupnosti se integrují do portálu. Pokud vyhodnocujete nový indexer, měli byste počítat s psaním kódu.

## <a name="permissions"></a>Oprávnění

Všechny operace související s indexery, včetně požadavků GET pro stav nebo definice, vyžadují [klíč rozhraní API pro správu](search-security-api-keys.md).

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Podporované zdroje dat

Úložiště dat procházení indexerů v Azure

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (ve verzi Preview)
* [Table Storage Azure](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Spravovaná instance SQL](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
* [SQL Server v Azure Virtual Machines](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

## <a name="indexer-stages"></a>Fáze indexeru

Pokud je index při počátečním spuštění prázdný, načtou se indexer všechna data, která jsou uvedena v tabulce nebo kontejneru. Při následném spuštění může indexer obvykle detekovat a načítat pouze data, která se změnila. V případě dat objektů BLOB je zjišťování změn automatické. U jiných zdrojů dat, jako je Azure SQL nebo Cosmos DB, musí být povolené zjišťování změn.

Pro každý dokument, který ingestuje, indexer implementuje nebo koordinuje více kroků od načtení dokumentu do konečného vyhledávacího modulu pro indexování. V případě potřeby je indexer také instrumentací pro dovednosti provádění a výstupy za předpokladu, že je definována dovednosti.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Fáze indexeru" border="false":::

### <a name="stage-1-document-cracking"></a>Fáze 1: trhliny dokumentů

Trhlina dokumentu je proces otevírání souborů a extrahování obsahu. V závislosti na typu zdroje dat se indexer pokusí provést různé operace, aby mohl extrahovat potenciálně indexovaný obsah.  

Příklady:  

* Když je dokument záznamem ve [zdroji dat SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), indexer extrahuje všechna pole záznamu.
* Když je dokument souborem PDF ve [zdroji dat BLOB Storage Azure](search-howto-indexing-azure-blob-storage.md), indexer vyextrahuje text, obrázky a metadata souboru.
* Když je dokument záznamem v [Cosmos DB zdroji dat](search-howto-index-cosmosdb.md), indexer vyextrahuje pole a podpole z dokumentu Cosmos DB.

### <a name="stage-2-field-mappings"></a>Fáze 2: mapování polí 

Indexer extrahuje text ze zdrojového pole a odešle ho do cílového pole v indexu nebo v úložišti znalostí. Pokud se názvy polí a typy shodují, cesta je nejasná. Ve výstupu však můžete chtít jiné názvy nebo typy, v takovém případě je nutné sdělit indexeru, jak pole namapovat. Tento krok nastane po zaznamenání dokumentu, ale před transformacemi, pokud indexer čte ze zdrojových dokumentů. Při definování [mapování polí](search-indexer-field-mappings.md)je hodnota pole zdroj odesílána tak, jak je, do cílového pole bez úprav. Mapování polí jsou volitelná.

### <a name="stage-3-skillset-execution"></a>Fáze 3: spuštění dovednosti

Dovednosti provádění je volitelný krok, který vyvolá integrované nebo vlastní zpracování AI. Může být potřeba pro optické rozpoznávání znaků (OCR) ve formě analýzy obrázků nebo může být nutné přeložit jazyk. Bez ohledu na transformaci dovednosti provádění je místo, kde dojde k obohacení. Pokud indexer je kanál, můžete si [dovednosti](cognitive-search-defining-skillset.md) představit jako "kanál v rámci kanálu". Dovednosti má vlastní sekvenci kroků s názvem dovednosti.

### <a name="stage-4-output-field-mappings"></a>Fáze 4: mapování polí výstupu

Výstupem dovednosti je ve skutečnosti strom informací nazývaný obohacený dokument. Mapování polí pro výstup umožňují vybrat, které části tohoto stromu mají být namapovány na pole v indexu. Přečtěte si, jak [definovat mapování polí pro výstup](cognitive-search-output-field-mapping.md).

Stejně jako u mapování polí, která přidružuje doslovné hodnoty ze zdrojového do cílového pole, mapování polí výstupu říká indexeru, jak přidružit transformované hodnoty v rozšířeném dokumentu na cílová pole v indexu. Na rozdíl od mapování polí, která jsou považována za volitelnou, bude vždy nutné definovat mapování polí pro libovolný transformovaný obsah, který se musí nacházet v indexu.

Následující obrázek ukazuje vzorový indexer [relace ladění](cognitive-search-debug-session.md) fází indexeru: trhliny dokumentů, mapování polí, provádění dovednosti a mapování výstupních polí.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="Fáze indexeru" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-configuration-steps"></a>Postup základní konfigurace

Indexery můžou nabízet funkce, které jsou jedinečné pro daný zdroj dat. Z toho důvodu se budou některé aspekty konfigurace indexeru nebo zdroje dat lišit podle typu indexeru. Všechny indexery ale sdílejí stejné základní složení a požadavky. Níže najdete popis kroků společných pro všechny indexery.

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat
Indexer získá připojení ke zdroji dat z objektu *zdroje dat* . Definice zdroje dat poskytuje připojovací řetězec a případně přihlašovací údaje. Chcete-li vytvořit prostředek, zavolejte třídu [Create datasource](/rest/api/searchservice/create-data-source) REST API nebo [DataSource](/dotnet/api/microsoft.azure.search.models.datasource) .

Zdroje dat se konfigurují a spravují nezávisle na indexerech, které je používají, což znamená, že několik indexerů může používat zdroj dat k načtení více indexů současně.

### <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu
Indexer automatizuje některé úkoly související s příjmem dat, ale vytváření indexu k nim obvykle nepatří. K základním požadavkům patří předdefinovaný index s poli, která odpovídají polím v externím zdroji dat. Pole musí odpovídat názvu a datovému typu. Další informace o strukturování indexu najdete v tématu [vytvoření indexu (Azure Kognitivní hledání REST API)](/rest/api/searchservice/Create-Index) nebo [třídy indexu](/dotnet/api/microsoft.azure.search.models.index). Nápovědu k přidružení polí najdete v tématu [mapování polí v indexerech Azure kognitivní hledání](search-indexer-field-mappings.md).

> [!Tip]
> Přestože indexery nedokážou vygenerovat index za vás, může vám pomoct průvodce **importem dat** na portálu. Ve většině případů dokáže průvodce odvodit schéma indexu ze stávajících metadat ve zdroji a zobrazit předběžné schéma indexu, které můžete upravit přímo v aktivním průvodci. Po vytvoření indexu ve službě jsou další úpravy na portálu omezené hlavně na přidávání nových polí. K vytvoření, ale ne revidování, indexu zvažte použití průvodce. Praktickou výuku najdete v [průvodci portálem](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Krok 3: Vytvoření a naplánování indexeru
Definice indexeru je konstrukce, která spojuje všechny prvky související s přijímáním dat. Mezi požadované prvky patří zdroj dat a index. Mezi volitelné prvky patří mapování plánu a polí. Mapování polí je nepovinné pouze v případě, že zdrojová pole a indexová pole jasně odpovídají. Další informace o strukturování indexeru najdete v tématu [Vytvoření indexeru (Azure Kognitivní hledání REST API)](/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Spustit indexery na vyžádání

I když je běžné indexování běžné, lze indexer vyvolat také na vyžádání pomocí [příkazu Run](/rest/api/searchservice/run-indexer):

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> Když se rozhraní API spustí úspěšně, vyvolání indexeru se naplánuje, ale vlastní zpracování proběhne asynchronně. 

Stav indexeru můžete monitorovat na portálu nebo prostřednictvím rozhraní API pro získání stavu indexeru. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Získat stav indexeru

Můžete načíst stav a historii spouštění indexeru pomocí [příkazu Get indexer status](/rest/api/searchservice/get-indexer-status):

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

Odpověď obsahuje celkový stav indexeru, vyvolání posledního (nebo probíhajícího) indexeru a historii posledních vyvolání indexeru.

```output
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
```

Historie spouštění obsahuje až 50 posledních dokončených spuštění, která jsou seřazena v opačném chronologickém pořadí (takže poslední spuštění nastane jako první v odpovědi).

## <a name="next-steps"></a>Další kroky
Teď jste získali základní představu. V dalším kroku se zaměříme na požadavky a úlohy specifické pro různé typy zdrojů dat.

* [Azure SQL Database, spravovaná instance SQL nebo SQL Server na virtuálním počítači Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Table Storage Azure](search-howto-indexing-azure-tables.md)
* [Indexování objektů BLOB CSV pomocí indexeru Azure Kognitivní hledání BLOB](search-howto-index-csv-blobs.md)
* [Indexování objektů BLOB JSON pomocí indexeru Azure Kognitivní hledání BLOB](search-howto-index-json-blobs.md)