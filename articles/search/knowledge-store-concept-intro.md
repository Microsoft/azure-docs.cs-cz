---
title: Úvod do znalostní báze Knowledge Store (Preview) – Azure Search
description: Posílat obohacené dokumenty do služby Azure Storage, kde můžete zobrazit, změnit tvar a využívat obohacené dokumenty v Azure Search i v jiných aplikacích.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: overview
ms.date: 08/02/2019
ms.author: heidist
ms.openlocfilehash: 07a0d3ef8660d32d14b8339fb76fa296b1c9635b
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202997"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>Co je znalostní úložiště v Azure Search?

> [!Note]
> Znalostní databáze je ve verzi Preview a není určená pro produkční použití. Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není dostupná žádná podpora sady .NET SDK.
>

Znalostní báze je funkce Azure Search, která ukládá obohacené dokumenty a metadata vytvořená pomocí kanálu indexování založeného na AI [(vyhledávání rozpoznávání)](cognitive-search-concept-intro.md). Obohacený dokument je výstup kanálu vytvořený z obsahu, který byl extrahován, strukturovaný a analyzován pomocí prostředků v Cognitive Services. Ve standardním kanálu založeném na AI jsou obohacené dokumenty přechodné, používá se jenom při indexování a pak se zahodí. S úložištěm Knowledge Store se dokumenty ukládají pro následné vyhodnocení, průzkum a můžou se stát vstupy pro úlohy pro datovou vědu pro příjem dat. 

Pokud jste v minulosti použili vyhledávání rozpoznávání, již víte, že dovednosti slouží k přesunu dokumentu pomocí sekvence rozšíření. Výsledek může být Azure Search index nebo (novinka v této verzi Preview) projekce ve znalostní bázi Knowledge Store. Dva výstupy, vyhledávací index a znalostní obchod jsou od sebe fyzicky odlišné. Sdílejí stejný obsah, ale jsou uloženy a používány velmi různými způsoby.

V závislosti na tom, jak nakonfigurujete kanál, je úložiště znalostí fyzicky Azure Storage účet, buď jako Azure Table Storage, BLOB Storage, nebo obojí. Libovolný nástroj nebo proces, který se může připojit k Azure Storage může využívat obsah znalostní báze Knowledge Store.

Projekce jsou vaším mechanismem, který slouží ke strukturování dat ve znalostní bázi Store. Například prostřednictvím projekce můžete zvolit, zda je výstup uložen jako jeden objekt BLOB nebo kolekce souvisejících tabulek. Snadný způsob, jak zobrazit obsah v obchodě Knowledge Store, je prostřednictvím integrované [Průzkumník služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) pro Azure Storage.

![Znalostní úložiště v diagramu kanálu](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Znalostní úložiště v diagramu kanálu")

Chcete-li použít znalostní bázi Store `knowledgeStore` , přidejte element do dovednosti, který definuje krokové operace v kanálu indexování. Během provádění Azure Search v účtu úložiště Azure vytvoří prostor a projekty obohacených dokumentů s definicí vytvořenou v rámci kanálu.

## <a name="benefits-of-knowledge-store"></a>Výhody znalostní báze Knowledge Store

Znalostní báze Knowledge Store poskytuje strukturu, kontext a skutečný obsah – mohli z nestrukturovaných a částečně strukturovaných datových souborů, jako jsou objekty blob, soubory obrázků prošlé analýzou, nebo i strukturovaná data, která se přetvarují na nové formuláře. V [podrobném návodu](knowledge-store-howto.md) napsaném v této verzi Preview se můžete podívat, jak se má zhuštěný dokument JSON rozdělit do podstruktur, rekládat do nových struktur a jinak zpřístupnit pro podřízené procesy jako počítač. výukové a datové vědecké úlohy.

I když je užitečné zjistit, co může kanál indexování založený na AI způsobit, je skutečná síla úložiště znalostí možnost data tvarovat. Můžete začít se základní dovednosti a potom iterovat přes něj a přidat tak zvýšené úrovně struktury, které pak můžete zkombinovat do nových struktur, a to i v jiných aplikacích, než Azure Search.

Ve výčtu jsou výhody znalostní báze Knowledge Store následující:

+ Využívání obohacených dokumentů v [nástrojích pro analýzu a vytváření sestav](#tools-and-apps) kromě hledání. Power BI s Power Query je přesvědčivá volba, ale jakýkoli nástroj nebo aplikace, které se můžou připojit ke službě Azure Storage, můžou získat z úložiště znalostí, které vytvoříte.

+ Upřesnění kanálu indexování AI při ladění kroků a definicí dovednosti. Znalostní báze Knowledge Store vám ukáže produkt definice dovednosti v kanálu indexování AI. Pomocí těchto výsledků můžete navrhnout lepší dovednosti, protože vidíte přesně to, co vypadá rozšíření. Pomocí [Průzkumník služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) ve službě Azure Storage můžete zobrazit obsah znalostní báze Knowledge Store.

+ Natvarujte data do nových formulářů. Změna tvaru je kodifikována v dovednosti, ale v tomto bodě je to, že dovednosti může tuto funkci nyní poskytnout. [Dovednost Shaper](cognitive-search-skill-shaper.md) v Azure Search byla rozšířena tak, aby odpovídala této úloze. Změna tvaru umožňuje definovat projekci, která se zarovnává s zamýšleným použitím dat při zachování vztahů.

> [!Note]
> Není známo, že indexování založené na AI používá Cognitive Services? Azure Search se integruje s funkcemi Cognitive Services Vision a Language pro extrakci a obohacení zdrojových dat pomocí optického rozpoznávání znaků (OCR) přes soubory obrázků, rozpoznávání entit a extrakce klíčových frází z textových souborů a dalších. Další informace najdete v tématu [co je rozpoznávání rozpoznávání?](cognitive-search-concept-intro.md).

## <a name="create-a-knowledge-store"></a>Vytvoření úložiště znalostí

Znalostní databáze je součástí definice dovednosti. V této verzi Preview vytváření vyžaduje REST API, pomocí `api-version=2019-05-06-Preview` nebo průvodce **importem dat** na portálu.

Následující kód JSON Určuje `knowledgeStore`, který je součástí dovednosti, který je vyvolán indexerem (nezobrazený). Specifikace projekce v rámci `knowledgeStore` určuje, zda jsou tabulky nebo objekty vytvořeny ve službě Azure Storage.

Pokud už jste obeznámeni s indexováním na bázi AI, definice dovednosti určuje vytváření, organizaci a obsah jednotlivých obohacených dokumentů.

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
               
            ]      
        },
        { 
            "tables": [ 
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

## <a name="components-backing-a-knowledge-store"></a>Komponenty, které zálohují znalostní bázi Store

K vytvoření znalostní databáze potřebujete následující služby a artefakty.

### <a name="1---source-data"></a>1 – zdrojová data

Data nebo dokumenty, které chcete rozšířit, musí existovat ve zdroji dat Azure podporovaném Azure Search indexery: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

* [Azure Table storage](search-howto-indexing-azure-tables.md)

### <a name="2---azure-search-service"></a>2 Azure Search služba

Pro vytváření a konfiguraci objektů používaných pro obohacení dat je také potřeba služba Azure Search a REST API. REST API pro vytvoření znalostní databáze je `api-version=2019-05-06-Preview`.

Azure Search poskytuje funkci indexeru a indexery slouží k tomu, aby provedl celý proces celých, což vede k trvalým obohaceným dokumentům ve službě Azure Storage. Indexery používají zdroj dat, index a dovednosti, které jsou potřeba k vytvoření a naplnění znalostní databáze.

| Object | REST API | Popis |
|--------|----------|-------------|
| Zdroj dat | [Vytvoření zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Prostředek identifikující externí zdroj dat Azure, který poskytuje zdrojová data používaná k vytváření obohacených dokumentů.  |
| Dovednosti | [Create dovednosti (API-Version = 2019-05 -06-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Prostředek, který koordinuje používání [integrovaných dovedností](cognitive-search-predefined-skills.md) a [vlastní vnímání zkušeností](cognitive-search-custom-skill-interface.md) , které se v kanálu rozšíření používají během indexování. |
| index | [Vytvořit index](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schéma, které vyjadřuje index Azure Search. Pole v indexu jsou mapována na pole ve zdrojových datech nebo na pole vyráběná během fáze obohacení (například pole pro názvy organizací vytvořená rozpoznáváním entit). |
| indexer | [Create Indexer (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Prostředek definující součásti používané při indexování: včetně zdroje dat, dovednosti, přidružení polí ze zdrojových a zprostředkujících datových struktur do cílového indexu a samotného indexu. Spuštění indexeru je triggerem pro přijímání a obohacení dat. Výstupem je index hledání založený na schématu indexu, vyplněný zdrojovými daty, obohacený přes dovednosti.  |

### <a name="3---cognitive-services"></a>3 – Cognitive Services

Rozšíření určená ve dovednosti jsou vlastní nebo založená na funkcích Počítačové zpracování obrazu a jazyka v Cognitive Services. Při indexování prostřednictvím dovednosti se využívá funkce Cognitive Services. Dovednosti je složení dovedností a dovednosti jsou vázány na konkrétní Počítačové zpracování obrazu a jazykové funkce. Pokud chcete integrovat Cognitive Services, [připojíte prostředek Cognitive Services](cognitive-search-attach-cognitive-services.md) k dovednosti.

### <a name="4---storage-account"></a>4\. účet úložiště

V rámci účtu Azure Storage Azure Search vytvoří kontejner objektů BLOB nebo tabulky nebo obojí v závislosti na tom, jak nakonfigurujete projekce v rámci dovednosti. Pokud vaše data pocházejí z Azure Blob nebo Table Storage, už jste nastavili a mohli znovu použít účet úložiště. V opačném případě budete muset vytvořit účet služby Azure Storage. Tabulky a objekty ve službě Azure Storage obsahují obohacené dokumenty, které vytvořil kanál indexování založený na AI.

Účet úložiště je určený v dovednosti. V `api-version=2019-05-06-Preview`nástroji obsahuje definice dovednosti definici obchodu s poznatky, abyste mohli zadat informace o účtu.

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5 – přístup a využití

Jakmile rozšíření existují v úložišti, můžete použít jakýkoli nástroj nebo technologii, která se připojuje ke službě Azure Blob nebo Table Storage, k prozkoumávání, analýze nebo využívání obsahu. Následující seznam je začátek:

+ [Průzkumník služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) k zobrazení obohacené struktury dokumentu a obsahu. Vezměte ho jako základní nástroj pro zobrazení obsahu znalostní databáze.

+ [Power BI s Power Query](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e) pro dotazy v přirozeném jazyce nebo v případě, že máte číselná data, použijte nástroje pro vytváření sestav a analýzy.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) pro další manipulaci.

+ Azure Search index pro fulltextové vyhledávání obsahu, který jste nahlásili pomocí [vyhledávání rozpoznávání](cognitive-search-concept-intro.md).

## <a name="document-persistence"></a>Trvalost dokumentu

V rámci účtu úložiště se rozšíření dají vyjádřit jako tabulky ve službě Azure Table Storage nebo jako objekty v úložišti objektů BLOB v Azure. Odvolání těchto rozšíření po uložení lze použít jako zdroj pro načtení dat do jiných databází a nástrojů.

+ Table Storage je užitečné, pokud chcete, aby reprezentace dat v tabulkovém formátu zohledňující schéma. Chcete-li znovu tvarovat nebo znovu kombinovat prvky novými způsoby, poskytuje úložiště tabulek nezbytné členitost.

+ Úložiště objektů BLOB vytvoří jedno (včetně) reprezentace JSON každého dokumentu. Můžete použít možnosti úložiště v jednom dovednosti a získat tak celou škálu výrazů.

+ Azure Search uchovává obsah v indexu. Pokud se jedná o scénář, který nesouvisí s vyhledáváním, například pokud je váš cíl analýzou jiného nástroje, můžete index, který vytvoří kanál, odstranit. Ale můžete také zachovat index a použít vestavěný nástroj, jako je [Průzkumník služby Search](search-explorer.md) , jako třetí médium (za Průzkumník služby Storage a vaši analytickou aplikaci) pro interakci s obsahem.  

## <a name="inside-a-knowledge-store"></a>Ve znalostní bázi Knowledge Store

 *Projekce* definuje schéma a strukturu obohacení, které odpovídají zamýšlenému použití. Můžete definovat více projekcí, pokud máte aplikace, které data využívají v různých formátech a tvarech. 

Projekce můžou být nakloubované jako objekty nebo tabulky:

+ Jako objekt se projekce mapuje na úložiště objektů blob, kde se projekce uloží do kontejneru, ve kterém jsou objekty nebo hierarchické reprezentace ve formátu JSON pro scénáře, jako je například kanál pro datové vědy.

+ Projekcí se jako tabulka mapuje na úložiště tabulek. Tabulková reprezentace zachovává relace pro scénáře, jako jsou například analýzy dat nebo export, jako datové snímky pro strojové učení. Obohacené projekce je pak možné snadno importovat do jiných úložišť dat. 

Ve znalostní bázi můžete vytvořit více výčnělků, které budou vyhovovat různým constituencies ve vaší organizaci. Vývojář může potřebovat přístup k úplnému vyjádření formátu JSON obohaceného dokumentu, zatímco odborníci na data mohou chtít, aby odborníci na data a analytiky mohli podrobit a modulární datové struktury ve tvaru dovednosti.

Například pokud jeden z cílů procesu rozšíření je také vytvořit datovou sadu, která se používá pro výuku modelu, prochází data do úložiště objektů jedním ze způsobů, jak použít data v kanálech pro datové vědy. Případně, pokud chcete vytvořit rychlý Power BI řídicí panel na základě obohacených dokumentů, bude tabulková projekce dobře fungovat.

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

Pro účely učení doporučujeme bezplatnou službu, ale mějte na paměti, že počet bezplatných transakcí je omezený na 20 dokumentů za den a na předplatné.

Pokud používáte víc služeb, vytvořte si všechny služby ve stejné oblasti, aby se co nejlépe vyzpůsobily a minimalizovaly náklady. Neúčtují se vám šířka pásma pro příchozí data nebo odchozí data, která přecházejí do jiné služby ve stejné oblasti.

**Krok 1: [Vytvoření prostředku Azure Search](search-create-service-portal.md)** 

**Krok 2: [Vytvoření účtu služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Krok 3: [Vytvoření prostředku Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Krok 4: Začněte [s portálem](cognitive-search-quickstart-blob.md) nebo začněte [s ukázkovými daty pomocí Rest a předzálohovacího](knowledge-store-howto.md) programu.** 

Pomocí REST `api-version=2019-05-06-Preview` můžete vytvořit kanál založený na AI, který zahrnuje znalostní bázi Store. V nejnovější verzi Preview rozhraní API poskytuje `knowledgeStore` objekt dovednosti definici.

## <a name="takeaways"></a>Shrnutí

Znalostní báze Knowledge Store nabízí celou řadu výhod, včetně neomezeného využívání obohacených dokumentů ve scénářích jiných než hledání, řízení nákladů a Správa posunu v procesu rozšíření. Tyto funkce jsou k dispozici, stačí, když do dovednosti přidáte účet úložiště a použijete aktualizovaný jazyk výrazů, jak je popsáno v tématu [jak začít s úložištěm Knowledge Store](knowledge-store-howto.md). 

## <a name="next-steps"></a>Další kroky

Nejjednodušším přístupem k vytváření obohacených dokumentů je průvodce **importem dat** .

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Vyzkoušet rozpoznávání rozpoznávání v průvodci na portálu](cognitive-search-quickstart-blob.md)
