---
title: Znalostní báze úložiště představení a přehled (preview) – Azure Search
description: Odeslat bohatších možností dokumenty do úložiště Azure, kde můžete zobrazit, upravovat a využívat bohatších možností dokumentů ve službě Azure Search i v jiných aplikacích.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: overview
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 4a27e4d8f2fbaafe6d27a3e3cabd31aa715b9d80
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540739"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>Co je znalostní báze úložiště ve službě Azure Search?

> [!Note]
> Znalostní báze úložiště je ve verzi preview a není určen pro použití v produkčním prostředí. [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) tuto funkci poskytuje. Není dostupná podpora .NET SDK v současnosti.
>

Znalostní báze úložiště je volitelná funkce služby Azure Search, která ukládá bohatších možností dokumenty a metadata vytvořený kanál indexování založené na umělé Inteligenci [(kognitivního vyhledávání)](cognitive-search-concept-intro.md). Znalostní báze úložiště je zajištěná účtu služby Azure storage, který nakonfigurujete jako součást kanálu. Pokud povolená, vyhledávací služba používá tento účet úložiště pro ukládání do mezipaměti reprezentace každý dokument bohatších možností. 

Pokud jste použili v minulosti kognitivního vyhledávání, již víte, že dovednosti je možné přesunout dokument pomocí sekvence obohacení. Výsledkem může být indexu Azure Search nebo (nově ve verzi preview) projekce v úložišti znalostní báze.

Projekce jsou váš mechanismus pro strukturování dat. pro použití v aplikaci pro příjem. Můžete použít [Průzkumníka služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) vytvořené pro Azure storage nebo jakékoli aplikaci, která se připojuje k Azure storage, který otevírá nové možnosti pro využívání rozšiřují dokumenty. Mezi příklady patří datové vědy kanály a vlastní analýzy.

![Znalostní báze úložiště v diagramu kanálu](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "úložiště znalostní báze v diagramu kanálu")

Chcete-li použít znalostní báze úložiště, přidejte `knowledgeStore` element, který definuje podle jednotlivých kroků operace v kanálu služby indexování dovedností. Během provádění Azure Search vytvoří prostor v účtu služby Azure storage a vyplní definice a obsah vytvořený kanál.

## <a name="benefits-of-knowledge-store"></a>Výhody úložiště znalostní báze

Znalostní báze úložiště poskytuje je struktura, kontextu a skutečný obsah – úsporách získaných nestrukturovaných a částečně strukturovaná data soubory jako objekty BLOB, soubory obrázků, které prošly analysis, nebo dokonce strukturovaná data, která je tvar do nového formuláře. V [podrobného návodu](knowledge-store-howto.md) napsané pro tuto verzi preview, zobrazí se první ruky jak hustému dokumentu JSON je rozdělený do používání dílčích struktur rekonstituován do nových struktur a jinak k dispozici pro směru server-klient procesy, jako jsou machine learning a datové vědy úlohy.

I když je užitečné, pokud chcete zobrazit, co může vytvořit kanál indexování založené na umělé Inteligenci, skutečná síla znalostní báze úložiště je schopnost upravovat data. Mohou začínat základní dovednosti a potom iterovat přes něj přidejte zvýšení úrovně struktury, který potom můžete nakombinovat do nových struktur, použitelné v jiných aplikacích kromě Azure Search.

Ve výčtu, výhody znalostní báze úložiště patří následující:

+ Využívat bohatších možností dokumenty v [analýzy a generování sestav nástroje](#tools-and-apps) než vyhledávání. Power BI pomocí Power Query je atraktivní výběru, ale libovolného nástroje nebo aplikace, která se může připojit k úložišti Azure můžete vyžádat z úložiště znalostní báze, který vytvoříte.

+ Při ladění kroky a dovednosti definice upřesněte kanál indexování AI. Znalostní báze úložiště zobrazí produktu definici dovedností v kanál indexování AI. Tyto výsledky můžete navrhnout lepší dovednosti, protože je uvidí přesně tak, jak obohacení vypadat. Můžete použít [Průzkumníka služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) ve službě Azure storage, chcete-li zobrazit obsah znalostní báze úložiště.

+ Tvarování dat do nového formuláře. Měnící tvar je právně upraveny v dovednosti, ale podstatné je, dovedností teď můžete zadat tuto funkci. [Shaper dovednosti](cognitive-search-skill-shaper.md) ve službě Azure Search bylo rozšířeno tak, aby vyhovovaly této úlohy. Změna tvaru můžete zadat projekce, které v souladu s zamýšlené použití dat při zachování vztahy.

> [!Note]
> Ještě neznají založené na umělé Inteligenci indexování s využitím služeb Cognitive Services? Azure Search se integruje se s funkcemi Cognitive Services pro zpracování obrazu a jazyk pro extrakci a rozšiřovat zdroje dat pomocí optického rozpoznávání znaků (OCR) přes soubory obrázků, rozpoznávání entit a extrakce klíčových frází z textových souborů a další. Další informace najdete v tématu [novinky kognitivního vyhledávání?](cognitive-search-concept-intro.md).

## <a name="create-a-knowledge-store"></a>Vytvoření znalostní báze úložiště

Znalostní báze úložiště je součástí definice dovednosti. V této verzi preview, vytvoříte ho vyžaduje rozhraní REST API, pomocí `api-version=2019-05-06-Preview` nebo **importovat data** Průvodce na portálu.

Následující kód JSON Určuje `knowledgeStore`, který je součástí dovedností, které se vyvolá podle indexer (nezobrazení). Specifikace projekce v rámci `knowledgeStore` Určuje, zda se vytvoří tabulky nebo objekty ve službě Azure storage.

Pokud jste již obeznámeni s založené na umělé Inteligenci indexování, definice dovednosti určuje vytváření, organizace a obsah každého bohatších možností dokumentu.

```json
{
  "name": "my-new-skillset",
  "description": 
  "Example showing knowledgeStore placement, supported in api-version=2019-05-06-Preview. You need at least one skill, most likely a Shaper skill if you are modulating data structures.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }    
    ]     
    } 
}
```

## <a name="components-backing-a-knowledge-store"></a>Součástí záložní úložiště znalostní báze

Vytvoření znalostní báze úložiště, budete potřebovat následující služby a artefakty.

### <a name="1---source-data"></a>1 - zdroj dat

Data nebo dokumenty, které chcete rozšířit musí existovat ve zdroji dat Azure pomocí indexerů Azure Search nepodporuje: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

[Azure Table storage](search-howto-indexing-azure-tables.md) lze použít pro odchozích dat v úložišti znalostní báze, ale nelze použít jako zdroj pro vstupní data pro kanál indexování založené na umělé Inteligenci.

### <a name="2---azure-search-service"></a>2 – azure Search service

Budete také potřebovat službu Azure Search a rozhraní REST API k vytvoření a konfiguraci objektů používaných pro rozšiřování dat. Rozhraní REST API pro vytváření znalostní báze úložiště je `api-version=2019-05-06-Preview`.

Azure Search poskytuje funkci indexeru a indexery se používají k řízení celý proces end až do konce, což vede k trvalé, bohatších možností dokumentů ve službě Azure storage. Indexery používat zdroj dat, index a dovedností – které jsou požadovány pro vytvoření a naplnění úložiště znalostní báze.

| Object | REST API | Popis |
|--------|----------|-------------|
| Zdroj dat | [Vytvoření zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Prostředek, který identifikuje zdroj dat externí Azure poskytuje zdroje dat použitých k vytvoření bohatších možností dokumenty.  |
| Dovednosti | [Create Skillset (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Prostředek koordinace použití [integrované znalosti](cognitive-search-predefined-skills.md) a [vlastního kognitivních dovedností](cognitive-search-custom-skill-interface.md) používané rozšíření kanálu během indexování. |
| index | [Vytvoření indexu](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schéma vyjádření indexu Azure Search. Mapování polí v indexu pole ve zdrojových datech nebo pole vyrobenými během fáze rozšíření (například pole pro názvy organizací vytvořené rozpoznávání entit). |
| indexer | [Create Indexer (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Zdroj definice součástí použitých při indexování: včetně zdroje dat, dovedností, přidružení pole ze zdroje a struktury pracovních dat na cílový index a index, samotného. Spuštění indexeru je aktivační událost pro příjem dat a rozšíření. Výstup je index vyhledávání na základě schématu indexu, naplní se zdrojovými daty, rozšiřují prostřednictvím dovednosti.  |

### <a name="3---cognitive-services"></a>3 – cognitive Services

Obohacení podle dovedností jsou založeny na funkce pro počítačové zpracování obrazu a jazyka ve službě Cognitive Services. Funkce služby cognitive Services se využívá během indexování prostřednictvím vaše dovednosti. Dovedností se skládá ze dovednosti a znalosti, které jsou vázány na konkrétní funkce pro počítačové zpracování obrazu a jazyk. Integrace služeb Cognitive Services, budete [připojit prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md) k dovedností.

### <a name="4---storage-account"></a>4 - účet úložiště

V rámci vašeho účtu Azure Storage Azure Search vytvoří kontejner objektů Blob nebo tabulek, v závislosti na tom, jak nakonfigurovat dovedností. Pokud vaše data pochází z úložiště objektů Azure Blob nebo tabulek, jsou již nastavíte. V opačném případě je potřeba vytvořit účet úložiště Azure. Tabulek a objektů ve službě Azure storage obsahují bohatších možností dokumenty vytvořené na kanál indexování založené na AI.

V zkušenostech se zadal účet úložiště. V `api-version=2019-05-06-Preview`, dovednosti definice obsahuje definici znalostní báze úložiště může poskytnout informace o účtu.

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5 – přístup a používání

Jakmile obohacení existují v úložišti, libovolného nástroje nebo technologie, která se připojuje k Azure Blob nebo Table storage umožňuje prozkoumat, analyzovat nebo využívat obsah. V následujícím seznamu je spuštění:

+ [Průzkumník služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) Chcete-li zobrazit strukturu dokumentu bohatších možností a obsah. Vezměte v úvahu to jako nástroj směrný plán pro zobrazení obsahu úložiště znalostní báze.

+ [Power BI pomocí Power Query](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e) pro dotazy v přirozeném jazyce, nebo použijte generování sestav a analýzy nástroje používáte číselná data.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) pro další zpracování.

+ Index Azure Search pro fulltextové vyhledávání přes obsah, který jste indexovány pomocí [kognitivního vyhledávání](cognitive-search-concept-intro.md).

## <a name="document-persistence"></a>Trvalost dokumentu

V rámci účtu úložiště obohacení vyjádřit jako tabulky v rámci služby Azure Table storage nebo objekty ve službě Azure Blob storage. Připomínáme, že obohacení, po uložení, je možné jako zdroj k načtení dat do jiné databáze a nástroje,

+ Table storage je užitečné, pokud chcete s ohledem na schéma znázornění dat ve formě tabulky. Pokud chcete změnit tvar nebo opětovnému zkombinování prvky novými způsoby, Table storage poskytuje nezbytné členitosti.

+ Úložiště objektů BLOB vytvoří jeden kompletní reprezentaci JSON každého dokumentu. Obě možnosti jsou v jedné dovednosti můžete získat celou řadu výrazy.

+ Služba Azure Search se uchovávají obsah v indexu. Pokud váš scénář je hledání nesouvisející, například pokud je váš cíl analýzy v jiný nástroj, můžete odstranit index, který vytvoří kanál. Ale můžete také ponechat index a používat integrované nástroje, jako je [Průzkumníka služby Search](search-explorer.md) jako třetí střední (za Průzkumníka služby Storage a analýzy aplikací) pro interakci s vaším obsahem.

Spolu s obsahu dokumentu zahrnují bohatších možností dokumenty metadat, který vytvořil obohacení verze dovednosti.  

## <a name="inside-a-knowledge-store"></a>Uvnitř úložiště znalostní báze

Znalostní báze úložiště se skládá z mezipaměti poznámku a projekce. *Mezipaměti* je interně službou ukládat do mezipaměti výsledky z dovednosti a sledování změn. A *projekce* definuje schéma a struktura obohacení, které odpovídají zamýšlený účel použití. Existuje jedna mezipaměti za znalostní báze úložiště, ale více projekce. 

Mezipaměť je vždy kontejner objektů blob, ale může být projekce kloubové jako tabulky nebo objekty:

+ Jako objekt projekce mapy do úložiště objektů Blob, kde je uložen projekce do kontejneru, ve kterém jsou objekty nebo Hierarchická reprezentace ve formátu JSON pro scénáře, jako jsou kanálu vědeckého zpracování dat.

+ Jako tabulku projekce mapy Table Storage. Tabulkové reprezentace zachová relace pro scénáře, jako jsou analýzy dat nebo exportu jako datové rámce pro machine learning. Bohatších možností projekce pak lze snadno importovat do jiných datových úložišť. 

Můžete vytvořit více projekce ve znalostní bázi úložiště tak, aby vyhovovaly různých constituencies ve vaší organizaci. Vývojář může vyžadovat přístup ke úplnou reprezentaci JSON bohatších možností dokumentu, zatímco odborníci přes data nebo analytici může být vhodné detailní nebo modulární datové struktury upravená ve vaše dovednosti.

Například pokud je jedním z cílů procesu rozšiřování také vytvoření datové sady pro trénování modelu, projekce data do úložiště objektů by jeden ze způsobů použití dat v datových věd kanálů. Případně pokud chcete vytvořit rychlé řídicí panel Power BI podle bohatších možností dokumenty tabulkové projekce dobře fungoval.

<!---
## Data lifecycle and billing

Each time you run the indexer, the cache in Azure storage is updated if the skillset definition or underlying source data has changed. As input documents are edited or deleted, changes are propagated through the annotation cache to the projections, ensuring that your projected data is a current representation of your inputs at the end of the indexer run. 

Generally speaking, pipeline processing can be an all-or-nothing operation, but Azure Search can process incremental changes, which saves you time and money.

If a document is new or updated, all skills are run. If only the skillset changes, reprocessing is scoped to just those skills and documents affected by your edit.

### Changes to a skillset
Suppose that you have a pipeline composed of multiple skills, operating over a large body of static data (for example, scanned documents), that takes 8 hours and costs $200 to create the knowledge store. Now suppose you need to tweak one of the skills in the skillset. Rather than starting over, Azure Search can determine which skill is affected, and reprocess only that skill. Cached data and projections that are unaffected by the change remain intact in the knowledge store.

### Changes in the data
Scenarios can vary considerably, but let's suppose instead of static data, you have volatile data that changes between indexer invocations. Given no changes to the skillset, you are charged for processing the delta of new and modified document. The timestamp information varies by data source, but for illustration, in a Blob container, Azure Search looks at the `lastmodified` date to determine which blobs need to be ingested.

> [!Note]
> While you can edit the data in the projections, any edits will be overwritten on the next pipeline invocation, assuming the document in source data is updated. 

### Deletions

Although Azure Search creates and updates structures and content in Azure storage, it does not delete them. Projections and cached documents continue to exist even when the skillset is deleted. As the owner of the storage account, you should delete a projection if it is no longer needed. 

### Tips for development

+ Start small with a representative sample of your data as you make significant changes to skillset composition. As your design finalizes, you can slowly add more data during later-stage development, and then roll in the entire data set when you are comfortable with the pipeline composition.

+ Retain control over indexer invocation. Indexers can run on a schedule, which is helpful for solutions that are rolled into production, but less helpful if you are actively developing your pipeline. During development, avoid schedules so that you don’t lose track of cache or projection state. Once your solution is in production and skillset composition is static, you can put the indexer on a schedule to pick up routine changes in the external source data. 

-->

## <a name="where-do-i-start"></a>Kde mám začít?

Jsme bezplatnou službu pro účely výuky doporučujeme, ale mějte na paměti, že číslo bezplatné transakce je omezený na 20 dokumenty za den na jedno předplatné.

Pokud používáte víc služeb, vytvořte všechny služby ve stejné oblasti pro zajištění nejlepšího výkonu a minimalizovat náklady. Se neúčtují poplatky za šířku pásma pro příchozí data nebo odchozích dat, která přejde na jinou službu ve stejné oblasti.

**Krok 1: [Vytvoří prostředek služby Azure Search](search-create-service-portal.md)** 

**Krok 2: [Vytvoření účtu služby Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Krok 3: [Vytvoření prostředku služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Krok 4: [Začínáme s portálem](cognitive-search-quickstart-blob.md) - nebo - [začít pracovat s ukázkovými daty pomocí rozhraní REST a Postman](knowledge-store-howto.md)** 

Můžete použít REST `api-version=2019-05-06-Preview` k vytvoření kanálu založené na AI, obsahující úložiště znalostní báze. V nejnovější verzi preview rozhraní API, obsahuje objekt dovednosti `knowledgeStore` definice.

## <a name="takeaways"></a>Shrnutí

Znalostní báze úložiště nabízí celou řadu výhod, včetně, ale mimo jiné povoluje použití bohatších možností dokumenty ve scénářích než vyhledávání, ovládacích prvků nákladů a správa odchylek v procesu rozšíření. Tyto funkce jsou všechny k dispozici pro použití jednoduše tak, že přidává se účet úložiště pro vaše dovednosti a pomocí jazyka aktualizované výrazu, jak je popsáno v [jak začít pracovat s úložištěm znalostní báze](knowledge-store-howto.md). 

## <a name="next-steps"></a>Další postup

Nejjednodušším způsobem vytvoření bohatších možností dokumentů je prostřednictvím **importovat data** průvodce.

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Vyzkoušení kognitivního vyhledávání v portálu návodu](cognitive-search-quickstart-blob.md)
