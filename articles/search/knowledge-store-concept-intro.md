---
title: Úvod do znalostní báze Knowledge Store (Preview)
titleSuffix: Azure Cognitive Search
description: Posílat obohacené dokumenty do služby Azure Storage, kde můžete zobrazit, změnit tvar a využívat obohacené dokumenty v Azure Kognitivní hledání a v jiných aplikacích. Tato funkce je ve verzi Public Preview.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: aa32f671756b8ba7f17c25592b6a15b66de42b2c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790023"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Seznámení s znalostními obchody v Azure Kognitivní hledání

> [!IMPORTANT] 
> znalostní databáze je aktuálně ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje funkce ve verzi Preview. V současné době je omezená podpora portálu a žádná podpora sady .NET SDK.

Znalostní báze je funkce služby Azure Kognitivní hledání, která uchovává výstup [kanálu rozšíření AI](cognitive-search-concept-intro.md) pro pozdější analýzu nebo jiné zpracování dat. *Obohacený dokument* je výstup kanálu vytvořený z obsahu, který byl extrahován, strukturovaný a analyzován pomocí procesů AI. V standardním kanálu AI jsou obohacené dokumenty přechodné, používané jenom při indexování a pak se zahodí. Díky znalostnímu obchodu jsou rozšířené dokumenty zachované. 

Pokud jste v minulosti používali zkušenosti s rozpoznáváním s Azure Kognitivní hledání, už víte, že *dovednosti* přesouvá dokument v rámci posloupnosti rozšíření. Výsledkem může být index vyhledávání nebo (novinka v této verzi Preview) projekce ve znalostní bázi Knowledge Store. Tyto dva výstupy, vyhledávací index a úložiště ve znalostní bázi sdílejí stejný obsah, ale ukládají se a používají se velmi různými způsoby.

Znalostní báze Knowledge Store je fyzicky [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview), buď úložiště tabulek Azure, Azure Blob Storage, nebo obojí. Libovolný nástroj nebo proces, který se může připojit k Azure Storage může využívat obsah znalostní báze Knowledge Store.

![Znalostní úložiště v diagramu kanálu](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Znalostní úložiště v diagramu kanálu")

Chcete-li použít znalostní bázi Store, přidejte `knowledgeStore` element do dovednosti, který definuje krokové operace v kanálu indexování. Během provádění Azure Kognitivní hledání vytvoří prostor v účtu úložiště Azure a projekty obohacených dokumentů jako objekty blob nebo do tabulek v závislosti na vaší konfiguraci.

## <a name="benefits-of-knowledge-store"></a>Výhody znalostní báze Knowledge Store

Znalostní báze Knowledge Store poskytuje strukturu, kontext a skutečný obsah – mohli z nestrukturovaných a částečně strukturovaných datových souborů, jako jsou objekty blob, soubory obrázků prošlé analýzou, nebo i strukturovaná data, která se přetvarují na nové formuláře. V [podrobném návodu](knowledge-store-howto.md)se můžete podívat, jak se má zhuštěný dokument JSON rozdělit do podstruktur, rekládat do nových struktur a jinak zpřístupnit pro podřízené procesy, jako je Machine Learning a datové vědy. úlohy.

I když je užitečné zjistit, co kanál obohacení AI může, je skutečná síla úložiště znalostí možnost změnit tvar dat. Můžete začít se základní dovednosti a potom iterovat přes něj a přidat tak zvýšené úrovně struktury, které pak můžete zkombinovat do nových struktur, a to i v jiných aplikacích než Azure Kognitivní hledání.

Ve výčtu jsou výhody znalostní báze Knowledge Store následující:

+ Využívání obohacených dokumentů v [nástrojích pro analýzu a vytváření sestav](#tools-and-apps) kromě hledání. Power BI s Power Query je přesvědčivá volba, ale jakýkoli nástroj nebo aplikace, které se můžou připojit ke službě Azure Storage, můžou získat z úložiště znalostí, které vytvoříte.

+ Upřesnění kanálu indexování AI při ladění kroků a definicí dovednosti. Znalostní báze Knowledge Store vám ukáže produkt definice dovednosti v kanálu indexování AI. Pomocí těchto výsledků můžete navrhnout lepší dovednosti, protože vidíte přesně to, co vypadá rozšíření. Pomocí [Průzkumník služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) ve službě Azure Storage můžete zobrazit obsah znalostní báze Knowledge Store.

+ Natvarujte data do nových formulářů. Změna tvaru je kodifikována v dovednosti, ale v tomto bodě je to, že dovednosti může tuto funkci nyní poskytnout. [Shaper dovednosti](cognitive-search-skill-shaper.md) v Azure kognitivní hledání se rozšířily tak, aby odpovídaly této úloze. Změna tvaru umožňuje definovat projekci, která se zarovnává s zamýšleným použitím dat při zachování vztahů.

> [!Note]
> Novinka pro rozšíření AI a vnímání zkušeností? Azure Kognitivní hledání se integruje s funkcemi Cognitive Services Vision a Language pro extrakci a obohacení zdrojových dat pomocí optického rozpoznávání znaků (OCR) přes soubory obrázků, rozpoznávání entit a extrakce klíčových frází z textových souborů a dalších. Další informace najdete v tématu věnovaném [rozšíření AI v Azure kognitivní hledání](cognitive-search-concept-intro.md).

## <a name="creating-a-knowledge-store"></a>Vytváření úložiště znalostí

Znalostní databáze je součástí [dovednostiu](cognitive-search-working-with-skillsets.md), který je zase součástí [indexeru](search-indexer-overview.md). 

V této verzi Preview můžete vytvořit úložiště znalostí pomocí REST API a `api-version=2019-05-06-Preview`nebo pomocí průvodce **importem dat** na portálu.

### <a name="json-representation-of-a-knowledge-store"></a>Reprezentace ve znalostní bázi JSON pro úložiště znalostí

Následující JSON určuje `knowledgeStore`, který je součástí dovednosti, který je vyvolán indexerem (nezobrazený). Pokud jste již obeznámeni s rozšířením AI, dovednosti určuje vytváření, organizaci a obsah jednotlivých obohacených dokumentů. Dovednosti musí obsahovat alespoň jednu dovednost, pravděpodobně Shaperou dovednost, pokud pracujete se strukturou dat.

`knowledgeStore` se skládá z připojení a projekce. 

+ Připojení je účet úložiště ve stejné oblasti jako Azure Kognitivní hledání. 

+ Projekce můžou být tabulkové, objekty JSON nebo soubory JSON. `Tables` definují fyzický výraz obohacených dokumentů v úložišti tabulek Azure. `Objects` definovat fyzické objekty JSON ve službě Azure Blob Storage. `Files` jsou binární soubory jako obrázky, které byly extrahovány z dokumentu, který bude trvale uložen.

+ Projekce jsou kolekce objektů projekce, každý objekt projekce může obsahovat `tables`, `objects` a `files`. Obohacení projektů v rámci jedné projekce souvisejí i v případě, že jsou promítnuty napříč typy (tabulky, objekty a soubory). Projekce v rámci objektů projekce nesouvisejí a jsou nezávislé. Stejný tvar může být aross více objektů projekce.

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
               
            ], 
            "files": [

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
            ],
            "files": [
                
            ]  
        }        
    ]     
    } 
}
```

Tato ukázka neobsahuje žádné obrázky. Příklad použití souborů projekce naleznete v tématu [práce s projekcemi](knowledge-store-projection-overview.md).
### <a name="sources-of-data-for-a-knowledge-store"></a>Zdroje dat pro znalostní bázi Knowledge Store

Pokud je znalostní báze výstupem z kanálu obohacení AI, jaké jsou vstupy? Původní data, která chcete extrahovat, rozšířit a Uložit do úložiště Knowledge Store, můžou pocházet z libovolného zdroje dat Azure podporovaného indexery vyhledávání: 

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
| indexovacím | [Vytvořit index](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Schéma, které vyjadřuje index vyhledávání. Pole v indexu jsou mapována na pole ve zdrojových datech nebo na pole vyráběná během fáze obohacení (například pole pro názvy organizací vytvořená rozpoznáváním entit). |
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

## <a name="next-steps"></a>Další kroky

Znalostní báze Knowledge Store nabízí persistenci obohacených dokumentů, které jsou užitečné při navrhování dovednosti nebo vytváření nových struktur a obsahu pro použití všemi klientskými aplikacemi, které mají přístup k Azure Storagemu účtu.

Nejjednodušším přístupem k vytváření obohacených dokumentů je průvodce **importem dat** , ale můžete také použít příkaz post a REST API, který je užitečnější, pokud chcete získat přehled o způsobu vytváření a odkazů na objekty.

> [!div class="nextstepaction"]
> [Vytvoření úložiště znalostí pomocí portálu](knowledge-store-create-portal.md)
> [vytvoření znalostní báze pomocí metody post a rozhraní REST APi](knowledge-store-create-rest.md)
