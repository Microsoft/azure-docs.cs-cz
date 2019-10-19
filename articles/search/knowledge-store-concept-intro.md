---
title: Úvod do znalostní báze Knowledge Store (Preview) – Azure Search
description: Posílat obohacené dokumenty do služby Azure Storage, kde můžete zobrazit, změnit tvar a využívat obohacené dokumenty v Azure Search i v jiných aplikacích.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 08/02/2019
ms.author: heidist
ms.openlocfilehash: 8a0022ce429b1359d8771f5089589fc779b8a751
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554882"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>Co je znalostní úložiště v Azure Search?

> [!Note]
> Znalostní databáze je ve verzi Preview a není určená pro produkční použití. Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není dostupná žádná podpora sady .NET SDK.
>

Znalostní báze je funkce Azure Search, která uchovává výstup [kanálu rozšíření AI](cognitive-search-concept-intro.md) pro pozdější analýzu nebo jiné zpracování dat. *Obohacený dokument* je výstup kanálu vytvořený z obsahu, který byl extrahován, strukturovaný a analyzován pomocí procesů AI. V standardním kanálu AI jsou obohacené dokumenty přechodné, používané jenom při indexování a pak se zahodí. Díky znalostnímu obchodu jsou rozšířené dokumenty zachované. 

Pokud jste v minulosti používali dovednosti AI s Azure Search, už víte, že *dovednosti* přesouvá dokument v rámci posloupnosti rozšíření. Výsledkem může být index vyhledávání nebo (novinka v této verzi Preview) projekce ve znalostní bázi Knowledge Store. Tyto dva výstupy, vyhledávací index a úložiště ve znalostní bázi sdílejí stejný obsah, ale ukládají se a používají se velmi různými způsoby.

Znalostní báze Knowledge Store je fyzicky [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview), buď úložiště tabulek Azure, Azure Blob Storage, nebo obojí. Libovolný nástroj nebo proces, který se může připojit k Azure Storage může využívat obsah znalostní báze Knowledge Store.

![Znalostní úložiště v diagramu kanálu](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Znalostní úložiště v diagramu kanálu")

Projekce jsou vaším mechanismem, který slouží ke strukturování dat ve znalostní bázi Store. Například prostřednictvím projekce můžete zvolit, zda je výstup uložen jako jeden objekt BLOB nebo kolekce souvisejících tabulek. 

Chcete-li použít znalostní bázi Store, přidejte `knowledgeStore` element do dovednosti, který definuje krokové operace v kanálu indexování. Během provádění Azure Search v účtu úložiště Azure vytvoří prostor a projekty obohacených dokumentů jako objekty blob nebo do tabulek v závislosti na vaší konfiguraci.

## <a name="benefits-of-knowledge-store"></a>Výhody znalostní báze Knowledge Store

Znalostní báze Knowledge Store poskytuje strukturu, kontext a skutečný obsah – mohli z nestrukturovaných a částečně strukturovaných datových souborů, jako jsou objekty blob, soubory obrázků prošlé analýzou, nebo i strukturovaná data, která se přetvarují na nové formuláře. V [podrobném návodu](knowledge-store-howto.md) napsaném v této verzi Preview se můžete podívat, jak se má zhuštěný dokument JSON rozdělit do podstruktur, rekládat do nových struktur a jinak zpřístupnit pro podřízené procesy jako počítač. výukové a datové vědecké úlohy.

I když je užitečné zjistit, co může kanál indexování založený na AI způsobit, je skutečná síla úložiště znalostí možnost data tvarovat. Můžete začít se základní dovednosti a potom iterovat přes něj a přidat tak zvýšené úrovně struktury, které pak můžete zkombinovat do nových struktur, a to i v jiných aplikacích, než Azure Search.

Ve výčtu jsou výhody znalostní báze Knowledge Store následující:

+ Využívání obohacených dokumentů v [nástrojích pro analýzu a vytváření sestav](#tools-and-apps) kromě hledání. Power BI s Power Query je přesvědčivá volba, ale jakýkoli nástroj nebo aplikace, které se můžou připojit ke službě Azure Storage, můžou získat z úložiště znalostí, které vytvoříte.

+ Upřesnění kanálu indexování AI při ladění kroků a definicí dovednosti. Znalostní báze Knowledge Store vám ukáže produkt definice dovednosti v kanálu indexování AI. Pomocí těchto výsledků můžete navrhnout lepší dovednosti, protože vidíte přesně to, co vypadá rozšíření. Pomocí [Průzkumník služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) ve službě Azure Storage můžete zobrazit obsah znalostní báze Knowledge Store.

+ Natvarujte data do nových formulářů. Změna tvaru je kodifikována v dovednosti, ale v tomto bodě je to, že dovednosti může tuto funkci nyní poskytnout. [Dovednost Shaper](cognitive-search-skill-shaper.md) v Azure Search byla rozšířena tak, aby odpovídala této úloze. Změna tvaru umožňuje definovat projekci, která se zarovnává s zamýšleným použitím dat při zachování vztahů.

> [!Note]
> Není známo, že indexování založené na AI používá Cognitive Services? Azure Search se integruje s funkcemi Cognitive Services Vision a Language pro extrakci a obohacení zdrojových dat pomocí optického rozpoznávání znaků (OCR) přes soubory obrázků, rozpoznávání entit a extrakce klíčových frází z textových souborů a dalších. Další informace najdete v tématu [co je rozpoznávání rozpoznávání?](cognitive-search-concept-intro.md).

## <a name="creating-a-knowledge-store"></a>Vytváření úložiště znalostí

Znalostní databáze je součástí [dovednostiu](cognitive-search-working-with-skillsets.md), který je zase součástí [indexeru](search-indexer-overview.md). 

V této verzi Preview můžete vytvořit úložiště znalostí pomocí REST API a `api-version=2019-05-06-Preview` nebo pomocí průvodce **importem dat** na portálu.

### <a name="json-representation-of-a-knowledge-store"></a>Reprezentace ve znalostní bázi JSON pro úložiště znalostí

Následující JSON určuje `knowledgeStore`, který je součástí dovednosti, který je vyvolán indexerem (nezobrazený). Pokud jste již obeznámeni s rozšířením AI, dovednosti určuje vytváření, organizaci a obsah jednotlivých obohacených dokumentů. Dovednosti musí obsahovat alespoň jednu dovednost, pravděpodobně Shaperou dovednost, pokud pracujete se strukturou dat.

@No__t_0 se skládá z připojení a projekce. 

+ Připojení je účet úložiště ve stejné oblasti jako Azure Search. 

+ Projekce jsou páry tabulky – objekty. `Tables` definují fyzický výraz obohacených dokumentů v úložišti tabulek Azure. `Objects` definovat fyzické objekty v úložišti objektů BLOB v Azure.

```json
{
  "name": "my-new-skillset",
  "description": "Example showing knowledgeStore placement in a skillset.",
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

### <a name="sources-of-data-for-a-knowledge-store"></a>Zdroje dat pro znalostní bázi Knowledge Store

Pokud je znalostní báze výstupem z kanálu obohacení AI, jaké jsou vstupy? Původní data, která chcete extrahovat, rozšířit a Uložit do úložiště Knowledge Store, můžou pocházet z libovolného zdroje dat Azure podporovaného Azure Search indexery: 

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

* [Azure Table storage](search-howto-indexing-azure-tables.md)

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indexery a dovednosti vytvoříte extrahování a obohacení nebo transformaci tohoto obsahu v rámci úlohy indexování a následně výsledky uložíte do úložiště znalostí.

### <a name="rest-apis-used-in-creation-of-a-knowledge-store"></a>Rozhraní REST API používaná při vytváření úložiště znalostí

Rozšíření požadovaná pro vytvoření úložiště znalostí (Create dovednosti and CREATE indexer) mají jenom dvě rozhraní API. Jiná rozhraní API se používají tak, jak jsou.

| Objekt | Rozhraní REST API | Popis |
|--------|----------|-------------|
| Zdroj dat | [Vytvoření zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Prostředek identifikující externí zdroj dat Azure, který poskytuje zdrojová data používaná k vytváření obohacených dokumentů.  |
| dovednosti | [Create dovednosti (API-Version = 2019-05 -06-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Prostředek, který koordinuje používání [integrovaných dovedností](cognitive-search-predefined-skills.md) a [vlastní vnímání zkušeností](cognitive-search-custom-skill-interface.md) , které se v kanálu rozšíření používají během indexování. Dovednosti má definici `knowledgeStore` jako podřízený element. |
| indexovacím | [Vytvořit index](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schéma, které vyjadřuje index Azure Search. Pole v indexu jsou mapována na pole ve zdrojových datech nebo na pole vyráběná během fáze obohacení (například pole pro názvy organizací vytvořená rozpoznáváním entit). |
| indexer | [Vytvoření indexeru (rozhraní API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Prostředek definující součásti používané při indexování: včetně zdroje dat, dovednosti, přidružení polí ze zdrojových a zprostředkujících datových struktur do cílového indexu a samotného indexu. Spuštění indexeru je triggerem pro přijímání a obohacení dat. Výstupem je index hledání založený na schématu indexu, vyplněný zdrojovými daty, obohacený přes dovednosti.  |

### <a name="physical-composition-of-a-knowledge-store"></a>Fyzické složení úložiště znalostí

 *Projekce*, která je prvkem definice `knowledgeStore`, kloubuje schéma a strukturu výstupu tak, aby odpovídala zamýšlenému použití. Můžete definovat více projekcí, pokud máte aplikace, které data využívají v různých formátech a tvarech. 

Projekce můžou být nakloubované jako objekty nebo tabulky:

+ Jako objekt se projekce mapuje na úložiště objektů blob, kde se projekce uloží do kontejneru, ve kterém jsou objekty nebo hierarchické reprezentace ve formátu JSON pro scénáře, jako je například kanál pro datové vědy.

+ Projekcí se jako tabulka mapuje na úložiště tabulek. Tabulková reprezentace zachovává relace pro scénáře, jako jsou například analýzy dat nebo export, jako datové snímky pro strojové učení. Obohacené projekce je pak možné snadno importovat do jiných úložišť dat. 

Ve znalostní bázi můžete vytvořit více výčnělků, které budou vyhovovat různým constituencies ve vaší organizaci. Vývojář může potřebovat přístup k úplnému vyjádření formátu JSON obohaceného dokumentu, zatímco odborníci na data mohou chtít, aby odborníci na data a analytiky mohli podrobit a modulární datové struktury ve tvaru dovednosti.

Například pokud jeden z cílů procesu rozšíření je také vytvořit datovou sadu, která se používá pro výuku modelu, prochází data do úložiště objektů jedním ze způsobů, jak použít data v kanálech pro datové vědy. Případně, pokud chcete vytvořit rychlý Power BI řídicí panel na základě obohacených dokumentů, bude tabulková projekce dobře fungovat.

<a name="tools-and-apps"></a>

## <a name="connecting-with-tools-and-apps"></a>Připojení k nástrojům a aplikacím

Jakmile rozšíření existují v úložišti, můžete použít jakýkoli nástroj nebo technologii, která se připojuje ke službě Azure Blob nebo Table Storage, k prozkoumávání, analýze nebo využívání obsahu. Následující seznam je začátek:

+ [Průzkumník služby Storage](knowledge-store-view-storage-explorer.md) k zobrazení obohacené struktury dokumentu a obsahu. Vezměte ho jako základní nástroj pro zobrazení obsahu znalostní databáze.

+ [Power BI](knowledge-store-connect-power-bi.md) pro sestavy a analytické nástroje, pokud máte číselná data.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) pro další manipulaci.


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

<!-- ## Where do I start?

We recommend the Free service for learning purposes, but be aware that the number of free transactions is limited to 20 documents per day, per subscription.

When using multiple services, create all of your services in the same region for best performance and to minimize costs. You are not charged for bandwidth for inbound data or outbound data that goes to another service in the same region.

**Step 1: [Create an Azure Search resource](search-create-service-portal.md)** 

**Step 2: [Create an Azure storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Step 3: [Create a Cognitive Services resource](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Step 4: [Get started with the portal](cognitive-search-quickstart-blob.md) - or - [Get started with sample data using REST and Postman](knowledge-store-howto.md)** 

You can use REST `api-version=2019-05-06-Preview` to construct an AI-based pipeline that includes knowledge store. In the newest preview API, the Skillset object provides the `knowledgeStore` definition. -->

## <a name="next-steps"></a>Další kroky

Znalostní báze Knowledge Store nabízí persistenci obohacených dokumentů, které jsou užitečné při navrhování dovednosti nebo vytváření nových struktur a obsahu pro použití všemi klientskými aplikacemi, které mají přístup k Azure Storagemu účtu.

Nejjednodušším přístupem k vytváření obohacených dokumentů je průvodce **importem dat** , ale můžete také použít příkaz post a REST API, který je užitečnější, pokud chcete získat přehled o způsobu vytváření a odkazů na objekty.

> [!div class="nextstepaction"]
> [Vytvoření úložiště znalostí pomocí portálu](knowledge-store-create-portal.md) 
> [vytvoření znalostní báze pomocí metody post a rozhraní REST APi](knowledge-store-create-rest.md)
