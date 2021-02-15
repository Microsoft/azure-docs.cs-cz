---
title: Vytvoření indexeru
titleSuffix: Azure Cognitive Search
description: Nastavením vlastností indexeru určíte počátek a umístění dat. Můžete nastavit parametry pro úpravu chování modulu runtime.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 5381c12253f3f301099d469639cc75e390ebceff
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100360954"
---
# <a name="creating-indexers-in-azure-cognitive-search"></a>Vytváření indexerů v Azure Kognitivní hledání

Indexer vyhledávání poskytuje automatizované pracovní postupy pro přenos dokumentů a obsahu z externího zdroje dat do indexu vyhledávání v rámci vyhledávací služby. Jak jsme původně navrhli, extrahuje text a metadata ze zdroje dat Azure, zabalí dokumenty do formátu JSON a předá výsledné dokumenty do vyhledávacího modulu pro indexování. Od tohoto okamžiku se rozšířila podpora [rozšíření AI](cognitive-search-concept-intro.md) pro zpracování hloubkového obsahu. 

Použití indexerů významně snižuje množství a složitost kódu, který potřebujete napsat. Tento článek se zaměřuje na mechanismy vytváření indexeru jako přípravy pro pokročilejší práci s indexery specifickými pro zdroj a [dovednosti](cognitive-search-working-with-skillsets.md).

## <a name="whats-an-indexer-definition"></a>Co je definice indexeru?

Indexery se používají pro indexování založené na textu, které vydávají alfanumerický obsah ze zdrojových polí do polí indexu nebo zpracování na bázi AI, které analyzuje nerozlišený text pro strukturu nebo analyzuje obrázky pro text a informace a také přidává obsah do indexu. Následující definice indexu jsou typické z toho, co můžete vytvořit pro kterýkoli scénář.

### <a name="indexers-for-text-content"></a>Indexery pro textový obsah

Původní účel indexeru byl zjednodušit složitý proces načítání indexu tím, že poskytuje mechanismus pro připojení a čtení textu a číselného obsahu z polí ve zdroji dat, serializaci tohoto obsahu jako dokumentů JSON a předání těchto dokumentů do vyhledávacího modulu pro indexování. Toto je stále primární případ použití a pro tuto operaci budete muset vytvořit indexer s vlastnostmi definovanými v následujícím příkladu.

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```

**`name`** Vlastnosti, **`dataSourceName`** a **`targetIndexName`** jsou povinné a v závislosti na tom, jak vytvoříte indexer, musí zdroj dat i index ve službě existovat předtím, než můžete spustit indexer. 

**`parameters`** Vlastnost upraví chování běhu, například počet chyb, které se mají přijmout před selháním celé úlohy. Parametry jsou také způsob, jakým byste určili chování specifické pro zdroj. Pokud je zdrojem například úložiště objektů blob, můžete nastavit parametr, který filtruje přípony souborů: `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }` .

**`field mappings`** Vlastnost se používá k explicitnímu mapování polí source-to-destination, pokud se tato pole liší podle názvu nebo typu. Další vlastnosti (nezobrazuje se), které se používají k [určení plánu](search-howto-schedule-indexers.md), vytvoření indexeru v zakázaném stavu, nebo zadání [šifrovacího klíče](search-security-manage-encryption-keys.md) pro doplňující Šifrování neaktivních dat.

### <a name="indexers-for-ai-indexing"></a>Indexery pro indexování AI

Protože indexery jsou mechanismy, pomocí kterých vyhledávací služba zpřístupňuje odchozí požadavky, indexery se rozšířily tak, aby podporovaly rozšíření AI, přidání infrastruktury a objektů pro implementaci tohoto případu použití.

Všechny výše uvedené vlastnosti a parametry se vztahují na indexery, které provádějí obohacení AI. Následující vlastnosti jsou specifické pro rozšíření AI: **`skillSets`** , **`outputFieldMappings`** , **`cache`** (jenom verze Preview a REST). 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

Rozšíření AI překračuje rozsah tohoto článku. Další informace najdete v těchto článcích: [rozšíření AI](cognitive-search-concept-intro.md), [dovednosti v Azure kognitivní hledání](cognitive-search-working-with-skillsets.md)a [vytváření dovednosti (REST)](/rest/api/searchservice/create-skillset).

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>Zvolit klienta indexeru a vytvořit indexer

Až budete připraveni vytvořit indexer na vzdálené službě vyhledávání, budete potřebovat klienta vyhledávání ve formě nástroje, jako je například Azure Portal nebo post nebo kód, který vytvoří instanci klienta indexeru. Pro prvotní vývoj a testování testování konceptů doporučujeme Azure Portal nebo rozhraní REST API.

### <a name="permissions"></a>Oprávnění

Všechny operace související s indexery, včetně požadavků GET pro stav nebo definice, vyžadují v žádosti [klíč rozhraní API pro správu](search-security-api-keys.md) .

### <a name="limits"></a>Omezení

Všechny [úrovně služeb omezují](search-limits-quotas-capacity.md#indexer-limits) počet objektů, které můžete vytvořit. Pokud experimentování na bezplatné úrovni, můžete mít pouze 3 objekty každého typu a 2 minuty zpracování indexeru (včetně zpracování dovednosti).

### <a name="use-azure-portal-to-create-an-indexer"></a>Vytvoření indexeru pomocí Azure Portal

Portál nabízí dvě možnosti pro vytvoření indexeru: [**Průvodce importem dat**](search-import-data-portal.md) a **nového indexeru** , který poskytuje pole pro zadání definice indexeru. Průvodce je jedinečný v tom, že vytvoří všechny požadované prvky. Další přístupy vyžadují, abyste nastavili zdroj dat a index.

Následující snímek obrazovky ukazuje, kde najdete tyto funkce na portálu. 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="Indexer hotelů" border="true":::

### <a name="use-a-rest-client"></a>Použití klienta REST

Post i Visual Studio Code (s rozšířením pro Azure Kognitivní hledání) můžou fungovat jako klient indexeru. Pomocí kteréhokoli nástroje se můžete připojit k vaší vyhledávací službě a odeslat požadavky na [Vytvoření indexeru (REST)](/rest/api/searchservice/create-indexer) . K dispozici je řada kurzů a příkladů, které předvádějí klientům REST pro vytváření objektů. 

Pokud chcete získat informace o jednotlivých klientech, začněte jedním z těchto článků:

+ [Vytvoření indexu vyhledávání pomocí REST a předzálohovacího](search-get-started-rest.md)
+ [Začínáme s Visual Studio Code a Azure Kognitivní hledání](search-get-started-vs-code.md)

Nápovědu k formulování požadavků indexeru najdete v tématu [operace indexeru (REST)](/rest/api/searchservice/Indexer-operations) .

### <a name="use-an-sdk"></a>Použití sady SDK

Pro Kognitivní hledání sady SDK Azure implementují všeobecně dostupné funkce. V takovém případě můžete použít kteroukoli sadu SDK k vytvoření objektů souvisejících s indexerem. Všechny z nich poskytují **SearchIndexerClient** , který obsahuje metody pro vytváření indexerů a souvisejících objektů, včetně dovednosti.

| Azure SDK | Klient | Příklady |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [DotNetHowToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample. Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [Indexery](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>Spuštění indexeru

Indexer se spustí automaticky při vytváření indexeru ve službě. Jedná se o momentně pravdy, kde zjistíte, jestli dojde k chybám připojení ke zdroji dat, potížím s mapováním polí nebo k problémům s dovednosti. 

Existuje několik způsobů, jak spustit indexer:

+ Odešlete požadavek HTTP pro [Vytvoření indexeru](/rest/api/searchservice/create-indexer) nebo [aktualizaci indexeru](/rest/api/searchservice/update-indexer) pro přidání nebo změnu definice a spuštění indexeru.

+ Odešlete požadavek HTTP na [indexer spustit](/rest/api/searchservice/run-indexer) , aby se spustil indexer bez změn definice.

+ Spusťte program, který volá SearchIndexerClient metody pro vytvoření, aktualizaci nebo spuštění.

> [!NOTE]
> Aby se zabránilo okamžitému spuštění indexeru při vytváření, přidejte **`disabled=true`** do definice indexeru.

Alternativně vložte indexer [podle plánu](search-howto-schedule-indexers.md) , který bude volat zpracování v pravidelných intervalech. 

Naplánované zpracování se obvykle shoduje s potřebou přírůstkového indexování změněného obsahu. Logika detekce změn je funkce, která je integrovaná do zdrojových platforem. Indexer automaticky detekuje změny v kontejneru objektů BLOB. Pokyny k využití zjišťování změn v jiných zdrojích dat najdete v dokumentaci k indexeru pro konkrétní zdroje dat:

+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Table Storage Azure](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="change-detection-and-indexer-state"></a>Zjišťování změn a stav indexeru

Indexery mohou detekovat změny v podkladových datech a zpracovávat pouze nové nebo aktualizované dokumenty u každého spuštění indexeru. Pokud například stav indexeru říká, že spuštění proběhlo úspěšně s `0/0` zpracovanými dokumenty, znamená to, že indexer nenašel žádné nové nebo změněné řádky nebo objekty BLOB v podkladovém zdroji dat.

Způsob, jakým indexer podporuje detekci změn, se liší podle zdroje dat:

+ Azure Blob Storage, Azure Table Storage a Azure Data Lake Storage Gen2 každou aktualizaci objektu BLOB nebo řádku s datem a časem zarazítkem. Různé indexery využívají tyto informace k určení dokumentů, které se mají aktualizovat v indexu. Vestavěná detekce změn znamená, že indexer dokáže rozpoznávat nové a aktualizované dokumenty, a to bez další konfigurace, která je potřeba na vaší straně.

+ SQL Azure a Cosmos DB ve svých platformách poskytovat funkce pro detekci změn. V definici zdroje dat můžete zadat zásady detekce změn.

U rozsáhlých zátěží indexování také indexovací člen sleduje poslední dokument, který zpracoval, prostřednictvím interní "horní značky". Značka se nikdy nezveřejňuje v rozhraní API, ale interně indexer udržuje přehled o tom, kde se zastavil. Když indexování pokračuje, ať už prostřednictvím naplánovaného spuštění nebo volání na vyžádání, indexer odkazuje na horní značku, aby mohl pokračovat tam, kde skončil.

Pokud potřebujete vymazat horní značku a znovu indexovat, můžete použít možnost [resetovat indexer](https://docs.microsoft.com/rest/api/searchservice/reset-indexer). Pro další selektivní opakované indexování použijte [resetování dovedností](https://docs.microsoft.com/rest/api/searchservice/preview-api/reset-skills) nebo [resetování dokumentů](https://docs.microsoft.com/rest/api/searchservice/preview-api/reset-documents). Prostřednictvím rozhraní API pro resetování můžete vymazat vnitřní stav a také mezipaměť vyprázdnit, pokud jste povolili [přírůstkové obohacení](search-howto-incremental-index.md). Další informace a porovnání jednotlivých možností obnovení najdete v tématu [spuštění nebo resetování indexerů, dovedností a dokumentů](search-howto-run-reset-indexers.md).

## <a name="know-your-data"></a>Znát data

Indexery očekávají sadu tabelárních řádků, kde se každý řádek v indexu zobrazí jako úplný nebo částečný dokument hledání. Často existuje korespondence 1:1 mezi řádkem a výsledným vyhledávacím dokumentem, kde všechna pole v řádku nastavená pro každý dokument plně naplní. Indexery ale můžete použít ke generování pouze části dokumentu, například pokud k sestavení indexu používáte více indexerů nebo přístupů. 

Pro sloučení relačních dat do sady řádků byste měli vytvořit zobrazení SQL nebo vytvořit dotaz, který vrátí nadřazené a podřízené záznamy na stejném řádku. Například předdefinovaná ukázková datová sada pro hotely je databáze SQL, která má 50 záznamů (jeden pro každý Hotel) propojených se záznamy místností v související tabulce. Dotaz, který sloučí kolektivní data do sady řádků, vloží všechny informace o místnostech v dokumentech JSON v každém záznamu hotelu. Informace o vložené místnosti jsou vygenerovány dotazem, který používá klauzuli **for JSON auto** . Další informace o této technikě najdete v [definici dotazu, který vrací vložené JSON](index-sql-relational-data.md#define-a-query-that-returns-embedded-json). Toto je pouze jeden příklad; můžete najít další přístupy, které budou mít stejný účinek.

Kromě plochých dat je důležité vyžádat si pouze data, která lze prohledávat. Hledaná data jsou alfanumerické. Kognitivní hledání nemůže prohledávat binární data v libovolném formátu, i když může extrahovat a odvodit textové popisy souborů obrázků (viz [rozšíření AI](cognitive-search-concept-intro.md)) a vytvořit prohledávatelný obsah. Podobně použití obohacení AI může velký text analyzovat modely přirozeného jazyka, aby bylo možné najít strukturu nebo relevantní informace a generovat nový obsah, který můžete přidat do vyhledávacího dokumentu.

Vzhledem k tomu, že indexery neopravují problémy s daty, mohou být potřeba další formy čištění nebo manipulace s daty. Další informace najdete v dokumentaci k produktu [Azure Database](/azure/?product=databases).

## <a name="know-your-index"></a>Znát váš index

Odvolat tyto indexery, které přecházejí z vyhledávacích dokumentů do vyhledávacího modulu pro indexování. Stejně jako indexery mají vlastnosti, které určují chování při spuštění, schéma indexu má vlastnosti, které jsou v podstatě ovlivněné, jak jsou řetězce indexovány (pouze řetězce jsou analyzovány a s tokeny). V závislosti na přiřazení analyzátoru se indexované řetězce můžou lišit od toho, co jste předali. Můžete vyhodnotit účinky analyzátorů pomocí nástroje [analyzovat text (REST)](/rest/api/searchservice/test-analyzer). Další informace o analyzátorech najdete v tématu [analyzátory pro zpracování textu](search-analyzers.md).

V souvislosti s tím, jak indexery komunikují s indexem, indexer pouze kontroluje názvy a typy polí. Neexistuje žádný krok ověření, který zajišťuje správné zadání příchozího obsahu pro odpovídající vyhledávací pole v indexu. Jako ověřovací krok můžete spouštět dotazy na vyplněný index, který vrací celé dokumenty nebo vybraná pole. Další informace o dotazování obsahu indexu najdete v tématu [Vytvoření základního dotazu](search-query-create.md).

## <a name="next-steps"></a>Další kroky

+ [Plánování indexerů](search-howto-schedule-indexers.md)
+ [Definování mapování polí](search-indexer-field-mappings.md)
+ [Monitorovat stav indexeru](search-howto-monitor-indexers.md)
+ [Připojení s využitím spravovaných identit](search-howto-managed-identities-data-sources.md)