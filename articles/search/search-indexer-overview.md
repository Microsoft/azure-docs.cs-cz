---
title: Přehled indexeru
titleSuffix: Azure Cognitive Search
description: Procházení Azure SQL Database, spravované instance SQL, Azure Cosmos DB nebo Azure Storage k extrakci prohledávatelných dat a naplnění indexu služby Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: a274e96defa8b6b74c046923d87f198029399dd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100098091"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexery ve službě Azure Cognitive Search

*Indexer* ve službě Azure kognitivní hledání je prohledávací modul, který extrahuje prohledávatelný text a metadata z externího zdroje dat Azure a naplní index vyhledávání pomocí mapování polí mezi zdrojovými daty a vaším indexem. Tento přístup se někdy označuje jako "pull model", protože služba získává data v, aniž byste museli psát kód, který do indexu přidává data. Indexery také zadávají možnosti [obohacení AI](cognitive-search-concept-intro.md) kognitivní hledání a integrují externí zpracování obsahu en route do indexu.

Indexery jsou jenom Azure a jednotlivé indexery pro [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Table Storage](search-howto-indexing-azure-tables.md) a [BLOB Storage](search-howto-indexing-azure-blob-storage.md). Při konfiguraci indexeru zadáte zdroj dat (počátek) a také index (cíl). Několik zdrojů, jako je BLOB Storage, má další konfigurační vlastnosti, které jsou specifické pro daný typ obsahu.

Indexery můžete spouštět na vyžádání nebo podle plánu opakované aktualizace dat, který se spouští tak často, jak každých pět minut. Častější aktualizace vyžadují [model push](search-what-is-data-import.md) , který současně aktualizuje data v kognitivní hledání Azure i v externím zdroji dat.

## <a name="usage-scenarios"></a>Scénáře použití

Indexer můžete použít jako jediný způsob příjmu dat nebo jako součást kombinace techniků, které načítají a volitelně transformují nebo rozšiřují obsah podél. Následující tabulka shrnuje hlavní scénáře.

| Scenario |Strategie |
|----------|---------|
| Jeden zdroj dat | Tento model je nejjednodušší: jeden zdroj dat je jediným poskytovatelem obsahu pro index vyhledávání. Ze zdroje určíte jedno pole obsahující jedinečné hodnoty, které bude sloužit jako klíč dokumentu v indexu vyhledávání. Jedinečná hodnota bude použita jako identifikátor. Všechna ostatní zdrojová pole jsou namapována implicitně nebo explicitně na odpovídající pole v indexu. </br></br>Důležitou poznatkem je, že hodnota klíče dokumentu pochází ze zdrojových dat. Vyhledávací služba negeneruje klíčové hodnoty. Při dalších spuštěních se přidají příchozí dokumenty s novými klíči, zatímco příchozí dokumenty s existujícími klíči se buď sloučí, nebo přepíší, v závislosti na tom, jestli jsou indexová pole null nebo naplněná. |
| Více zdrojů dat | Index může přijímat obsah z více zdrojů, kde každé spuštění přináší nový obsah z jiného zdroje. </br></br>Jedním z výsledků může být index, který po spuštění každého indexeru získává dokumenty, přičemž všechny dokumenty jsou zcela vytvořené z každého zdroje. Například dokumenty 1-100 se nacházejí v úložišti objektů blob, dokumenty 101-200 jsou z Azure SQL a tak dále. Výzvou k tomuto scénáři spočívá v navrhování schématu indexu, který funguje pro všechna příchozí data, a strukturu klíčů dokumentu, která je v indexu vyhledávání jednotná. Nativně jsou hodnoty, které jedinečně identifikují dokument, metadata_storage_path v kontejneru objektů BLOB a v primárním klíči v tabulce SQL. Můžete si představit, že jeden nebo oba zdroje musí být upraveny tak, aby poskytovaly klíčové hodnoty ve společném formátu bez ohledu na původ obsahu. V tomto scénáři byste měli očekávat, že provedete určitou úroveň předběžného zpracování, aby se data homogenizuje, aby bylo možné je načíst do jediného indexu. </br></br>Alternativním výsledkem může být hledání dokumentů, které se částečně vyplňují při prvním spuštění, a potom se dále vyplní následnými běhy, aby se hodnoty z jiných zdrojů vyplnily. Například pole 1-10 se nacházejí v úložišti objektů blob, 11-20 z Azure SQL a tak dále. Výzvou k tomuto vzoru se zajistí, že každý běh indexování bude cílen na stejný dokument. Sloučení polí do existujícího dokumentu vyžaduje shodu s klíčem dokumentu. Ukázku tohoto scénáře najdete v tématu [kurz: index z více zdrojů dat](tutorial-multiple-data-sources.md). |
| Více indexerů | Pokud používáte více zdrojů dat, můžete také potřebovat více indexerů, pokud potřebujete měnit parametry doby běhu, plán nebo mapování polí. I když může stejný index cílit více sad zdrojů dat indexeru, buďte opatrní při spuštění indexeru, které může přepsat existující hodnoty v indexu. Pokud druhý indexer – zdroj dat cílí na stejné dokumenty a pole, budou přepsány všechny hodnoty z prvního spuštění. Hodnoty polí jsou v plném rozsahu nahrazeny. Indexer nemůže sloučit hodnoty z více běhů do stejného pole.</br></br>Dalším případem použití více indexerů je [horizontální horizontální navýšení kapacity kognitivní hledání](search-performance-optimization.md#use-indexers-for-updating-content-on-multiple-services). Můžete mít kopie stejného indexu hledání v různých oblastech. Chcete-li synchronizovat obsah indexu hledání, můžete mít více indexerů ze stejného zdroje dat, kde každý indexer cílí na jiný index vyhledávání.</br></br>[Paralelní indexování](search-howto-large-index.md#parallel-indexing) velmi velkých datových sad vyžaduje také strategii s více indexery. Každý indexer cílí na podmnožinu dat. |
| Transformace obsahu | Kognitivní hledání podporuje volitelná chování [rozšíření AI](cognitive-search-concept-intro.md) , která přidávají analýzu obrázků a zpracování přirozeného jazyka pro vytváření nových prohledávatelných obsahu a struktury. Rozšíření AI je založené na indexerech prostřednictvím připojeného [dovednosti](cognitive-search-working-with-skillsets.md). Aby bylo možné provést rozšíření AI, indexer stále potřebuje index a zdroj dat Azure, ale v tomto scénáři přidá zpracování dovednosti do indexeru. |

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Podporované zdroje dat

Úložiště dat procházení indexerů v Azure

+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (ve verzi Preview)
+ [Table Storage Azure](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Spravovaná instance SQL](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [SQL Server na Azure Virtual Machines](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

Připojení indexeru ke vzdáleným zdrojům dat se dají provádět pomocí standardních připojení k Internetu (veřejných) nebo šifrovaných privátních připojení, když používáte Azure Virtual Networks pro klientské aplikace. Můžete také nastavit připojení pro ověřování pomocí identity důvěryhodné služby. Další informace o zabezpečených připojeních najdete v tématu [udělení přístupu přes soukromé koncové body](search-indexer-securing-resources.md#granting-access-via-private-endpoints) a [připojení ke zdroji dat pomocí spravované identity](search-howto-managed-identities-data-sources.md).

## <a name="stages-of-indexing"></a>Fáze indexování

Pokud je index při počátečním spuštění prázdný, načtou se indexer všechna data, která jsou uvedena v tabulce nebo kontejneru. Při následném spuštění může indexer obvykle detekovat a načítat pouze data, která se změnila. V případě dat objektů BLOB je zjišťování změn automatické. U jiných zdrojů dat, jako je Azure SQL nebo Cosmos DB, musí být povolené zjišťování změn.

Pro každý dokument, který obdrží, indexer implementuje nebo koordinuje více kroků od načtení dokumentu do konečného vyhledávacího modulu pro indexování. V případě potřeby je indexer také instrumentací pro dovednosti provádění a výstupy za předpokladu, že je definována dovednosti.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Fáze indexeru" border="false":::

### <a name="stage-1-document-cracking"></a>Fáze 1: trhliny dokumentů

Trhlina dokumentu je proces otevírání souborů a extrahování obsahu. V závislosti na typu zdroje dat se indexer pokusí provést různé operace, aby mohl extrahovat potenciálně indexovaný obsah.  

Příklady:  

+ Když je dokument záznamem ve [zdroji dat SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), indexer extrahuje všechna pole záznamu.
+ Když je dokument souborem PDF ve [zdroji dat BLOB Storage Azure](search-howto-indexing-azure-blob-storage.md), indexer bude extrahovat text, obrázky a metadata.
+ Když je dokument záznamem v [Cosmos DB zdroji dat](search-howto-index-cosmosdb.md), indexer vyextrahuje pole a podpole z dokumentu Cosmos DB.

### <a name="stage-2-field-mappings"></a>Fáze 2: mapování polí 

Indexer extrahuje text ze zdrojového pole a odešle ho do cílového pole v indexu nebo v úložišti znalostí. Pokud se názvy polí a typy shodují, cesta je nejasná. Ve výstupu však můžete chtít jiné názvy nebo typy, v takovém případě je nutné sdělit indexeru, jak pole namapovat. 

Tento krok nastane po zaznamenání dokumentu, ale před transformacemi, pokud indexer čte ze zdrojových dokumentů. Při definování [mapování polí](search-indexer-field-mappings.md)je hodnota pole zdroj odesílána tak, jak je, do cílového pole bez úprav. 

### <a name="stage-3-skillset-execution"></a>Fáze 3: spuštění dovednosti

Dovednosti provádění je volitelný krok, který vyvolá integrované nebo vlastní zpracování AI. Je možné, že ho budete potřebovat pro optické rozpoznávání znaků (OCR) ve formě analýzy obrázků, pokud jsou zdrojová data binárním obrázkem, nebo pokud je obsah v různých jazycích, může být nutné překlad jazyka. 

Bez ohledu na transformaci dovednosti provádění je místo, kde dojde k obohacení. Pokud indexer je kanál, můžete si [dovednosti](cognitive-search-defining-skillset.md) představit jako "kanál v rámci kanálu".

### <a name="stage-4-output-field-mappings"></a>Fáze 4: mapování polí výstupu

Pokud zahrnete dovednosti, pravděpodobně budete muset zahrnout mapování polí výstupu. Výstupem dovednosti je ve skutečnosti strom informací nazývaný *obohacený dokument*. Mapování polí pro výstup umožňují vybrat, které části tohoto stromu mají být namapovány na pole v indexu. Přečtěte si, jak [definovat mapování polí pro výstup](cognitive-search-output-field-mapping.md).

Vzhledem k tomu, že mapování polí přiřadí doslovné hodnoty ze zdroje dat do cílových polí, mapování polí výstupu sděluje indexeru, jak přidružit transformované hodnoty v rozšířeném dokumentu na cílová pole v indexu. Na rozdíl od mapování polí, která jsou považována za volitelnou, bude vždy nutné definovat mapování polí pro libovolný transformovaný obsah, který se musí nacházet v indexu.

Následující obrázek ukazuje vzorový indexer [relace ladění](cognitive-search-debug-session.md) fází indexeru: trhliny dokumentů, mapování polí, provádění dovednosti a mapování výstupních polí.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="Ukázková relace ladění" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>Základní pracovní postup

Indexery můžou nabízet funkce, které jsou jedinečné pro daný zdroj dat. Z toho důvodu se budou některé aspekty konfigurace indexeru nebo zdroje dat lišit podle typu indexeru. Všechny indexery ale sdílejí stejné základní složení a požadavky. Níže najdete popis kroků společných pro všechny indexery.

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

Indexery vyžadují objekt *zdroje dat* , který poskytuje připojovací řetězec a případně přihlašovací údaje. Pro vytvoření prostředku zavolejte třídu [Create data source (REST)](/rest/api/searchservice/create-data-source) nebo [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) .

Zdroje dat se konfigurují a spravují nezávisle na indexerech, které je používají, což znamená, že několik indexerů může používat zdroj dat k načtení více indexů současně.

### <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu

Indexer automatizuje některé úkoly související s příjmem dat, ale vytváření indexu k nim obvykle nepatří. K základním požadavkům patří předdefinovaný index s poli, která odpovídají polím v externím zdroji dat. Pole musí odpovídat názvu a datovému typu. V takovém případě můžete [definovat mapování polí](search-indexer-field-mappings.md) pro vytvoření přidružení. Další informace o strukturování indexu naleznete v tématu [Create a index (REST)](/rest/api/searchservice/Create-Index) nebo [SearchIndex Class](/dotnet/api/azure.search.documents.indexes.models.searchindex).

> [!Tip]
> Přestože indexery nedokážou vygenerovat index za vás, může vám pomoct průvodce **importem dat** na portálu. Ve většině případů dokáže průvodce odvodit schéma indexu ze stávajících metadat ve zdroji a zobrazit předběžné schéma indexu, které můžete upravit přímo v aktivním průvodci. Po vytvoření indexu ve službě jsou další úpravy na portálu omezené hlavně na přidávání nových polí. K vytvoření, ale ne revidování, indexu zvažte použití průvodce. Praktickou výuku najdete v [průvodci portálem](search-get-started-portal.md).

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>Krok 3: vytvoření a spuštění (nebo plánování) indexeru

Indexer se spustí při prvním [Vytvoření indexeru](/rest/api/searchservice/Create-Indexer) ve vyhledávací službě. Je to jenom tehdy, když vytvoříte nebo spustíte indexer, který zjistíte, jestli je zdroj dat dostupný, nebo že je dovednosti platný. Po prvním spuštění můžete ho na vyžádání znovu spustit pomocí [indexeru spustit](/rest/api/searchservice/run-indexer), nebo můžete [definovat plán opakování](search-howto-schedule-indexers.md). 

Stav indexeru můžete monitorovat na portálu nebo prostřednictvím [rozhraní API pro získání stavu indexeru](/rest/api/searchservice/get-indexer-status). Měli byste taky [spouštět dotazy na indexu](search-query-create.md) , abyste ověřili, že výsledek je to, co jste očekávali.

## <a name="next-steps"></a>Další kroky

Teď, když jste zavedli, je dalším krokem Kontrola vlastností a parametrů indexeru, plánování a monitorování indexerů. Případně se můžete vrátit do seznamu [podporovaných zdrojů dat](#supported-data-sources) , kde najdete další informace o konkrétním zdroji.

+ [Vytváření indexerů](search-howto-create-indexers.md)
+ [Resetování a spuštění indexerů](search-howto-run-reset-indexers.md)
+ [Plánování indexerů](search-howto-schedule-indexers.md)
+ [Definování mapování polí](search-indexer-field-mappings.md)
+ [Monitorovat stav indexeru](search-howto-monitor-indexers.md)