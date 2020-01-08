---
title: Úvod do znalostní báze Knowledge Store (Preview)
titleSuffix: Azure Cognitive Search
description: Posílání obohacených dokumentů na Azure Storage, kde můžete zobrazit, změnit tvar a využívat obohacené dokumenty v Azure Kognitivní hledání a v jiných aplikacích. Tato funkce je ve verzi Public Preview.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 2cde70579e61d1911008f7c8126ad7bda132eac1
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563492"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Seznámení s znalostními obchody v Azure Kognitivní hledání

> [!IMPORTANT] 
> znalostní databáze je aktuálně ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje funkce ve verzi Preview. V současné době je omezená podpora portálu a žádná podpora sady .NET SDK.

Znalostní báze je funkce služby Azure Kognitivní hledání, která uchovává výstup [kanálu rozšíření AI](cognitive-search-concept-intro.md) pro nezávislou analýzu nebo zpracování pro příjem dat. *Obohacený dokument* je výstup kanálu vytvořený z obsahu, který byl extrahován, strukturovaný a analyzován pomocí procesů AI. V standardním kanálu AI jsou obohacené dokumenty přechodné, používané jenom při indexování a pak se zahodí. Díky znalostnímu obchodu jsou rozšířené dokumenty zachované. 

Pokud jste v minulosti používali rozpoznávání vnímání, již víte, že *dovednosti* přesouvá dokument v rámci posloupnosti rozšíření. Výsledkem může být index vyhledávání nebo (novinka v této verzi Preview) projekce ve znalostní bázi Knowledge Store. Dva výstupy, vyhledávací index a znalostní obchod jsou produkty stejného kanálu. odvozeno ze stejných vstupů, ale vede výstup, který je strukturovaný, uložený a používá se velmi různými způsoby.

Znalostní báze Knowledge Store je fyzicky [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview), buď úložiště tabulek Azure, Azure Blob Storage, nebo obojí. Libovolný nástroj nebo proces, který se může připojit k Azure Storage může využívat obsah znalostní báze Knowledge Store.

![Znalostní úložiště v diagramu kanálu](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Znalostní úložiště v diagramu kanálu")

## <a name="benefits-of-knowledge-store"></a>Výhody znalostní báze Knowledge Store

Znalostní báze poskytuje strukturu, kontext a skutečný obsah – mohli z nestrukturovaných a částečně strukturovaných datových souborů, jako jsou objekty blob, soubory obrázků, které prošly analýzou, nebo dokonce strukturovaná data, která se přetvarují na nové formuláře. V [podrobném návodu](knowledge-store-howto.md)se můžete podívat, jak se má zhuštěný dokument JSON rozdělit do podstruktur, rekládat do nových struktur a jinak zpřístupnit pro podřízené procesy, jako jsou Machine Learning a úlohy datových věd.

I když je užitečné zjistit, co kanál pro obohacení AI může vytvořit, skutečný potenciál úložiště Knowledge Store je možnost změnit tvarování dat. Můžete začít se základní dovednosti a potom iterovat přes něj a přidat tak zvýšené úrovně struktury, které pak můžete zkombinovat do nových struktur, a to i v jiných aplikacích než Azure Kognitivní hledání.

Ve výčtu jsou výhody znalostní báze Knowledge Store následující:

+ Využívání obohacených dokumentů v [nástrojích pro analýzu a vytváření sestav](#tools-and-apps) kromě hledání. Power BI s Power Query je přesvědčivá volba, ale jakýkoli nástroj nebo aplikace, které se můžou připojit k Azure Storage, můžou vyžádat z úložiště, které vytvoříte.

+ Upřesnění kanálu indexování AI při ladění kroků a definicí dovednosti. Znalostní báze Knowledge Store vám ukáže produkt definice dovednosti v kanálu indexování AI. Pomocí těchto výsledků můžete navrhnout lepší dovednosti, protože vidíte přesně to, co vypadá rozšíření. V Azure Storage můžete použít [Průzkumník služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) k zobrazení obsahu úložiště Knowledge Store.

+ Natvarujte data do nových formulářů. Změna tvaru je kodifikována v dovednosti, ale v tomto bodě je to, že dovednosti může tuto funkci nyní poskytnout. [Shaper dovednosti](cognitive-search-skill-shaper.md) v Azure kognitivní hledání se rozšířily tak, aby odpovídaly této úloze. Změna tvaru umožňuje definovat projekci, která se zarovnává s zamýšleným použitím dat při zachování vztahů.

> [!Note]
> Novinka pro rozšíření AI a vnímání zkušeností? Azure Kognitivní hledání se integruje s funkcemi Cognitive Services Vision a Language pro extrakci a obohacení zdrojových dat pomocí optického rozpoznávání znaků (OCR) přes soubory obrázků, rozpoznávání entit a extrakce klíčových frází z textových souborů a dalších. Další informace najdete v tématu věnovaném [rozšíření AI v Azure kognitivní hledání](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Fyzické úložiště

Fyzický výraz úložiště znalostí je kloubem prvku `projections` definice `knowledgeStore` v dovednosti. Projekce definuje strukturu výstupu tak, aby odpovídala zamýšlenému použití.

Projekce lze nakloubovat jako tabulky, objekty nebo soubory.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

Typ projekce, kterou zadáte v této struktuře, určuje typ úložiště používaný službou Knowledge Store.

+ Tabulka úložiště se používá při definování `tables`. Definujte projekci tabulky, když potřebujete struktury tabulkového vytváření sestav pro vstupy do analytických nástrojů nebo exportovat jako datové snímky do jiných úložišť dat. Můžete zadat více `tables` pro získání podmnožiny nebo průřezu obohacených dokumentů. V rámci stejné skupiny projekce jsou vztahy mezi tabulkami zachované, takže můžete pracovat se všemi nimi.

+ Úložiště objektů BLOB se používá při definování `objects` nebo `files`. Fyzická reprezentace `object` je hierarchická struktura JSON, která představuje obohacený dokument. `file` je obrázek extrahovaný z dokumentu, který se přenese beze změny do úložiště objektů BLOB.

Jeden objekt projekce obsahuje jednu sadu `tables`, `objects`, `files`a pro mnoho scénářů, což může být stačit pro vytvoření jedné projekce. 

Je ale možné vytvořit několik sad `table`-`object`-`file` projekce a můžete to udělat, pokud chcete použít různé datové vztahy. V rámci sady se data vztahují, za předpokladu, že tyto relace existují a lze je zjistit. Pokud vytvoříte další sady, dokumenty v každé skupině se nikdy netýkají. Příkladem použití více skupin projekce může být, pokud chcete, aby stejná data byla promítnuta pro použití s vaším online systémem, a je třeba, aby se stejná data využívala v kanálu pro datové vědy, který je reprezentován liší.

## <a name="requirements"></a>Požadavky 

[Azure Storage](https://docs.microsoft.com/azure/storage/) se vyžaduje. Poskytuje fyzické úložiště. Můžete použít úložiště objektů blob, tabulkové úložiště nebo obojí. Úložiště objektů BLOB se používá pro nedotčené rozšířené dokumenty, většinou když výstup probíhá na navazující procesy. Table Storage je pro řezy obohacených dokumentů, které se běžně používají k analýze a vytváření sestav.

[Dovednosti](cognitive-search-working-with-skillsets.md) je povinný. Obsahuje definici `knowledgeStore` a určuje strukturu a složení obohaceného dokumentu. Nemůžete vytvořit úložiště znalostí pomocí prázdného dovednosti. Musíte mít aspoň jednu dovednost v dovednosti.

[Indexer](search-indexer-overview.md) je povinný. Dovednosti je vyvolán indexerem, který řídí spuštění. Indexery jsou dodávány s vlastní sadou požadavků a atributů. Některé z těchto atributů mají přímý vliv na znalostní bázi Knowledge Store:

+ Indexery vyžadují [podporovaný zdroj dat Azure](search-indexer-overview.md#supported-data-sources) (kanál, který nakonec vytvoří službu Knowledge Store, začne vyvoláním dat z podporovaného zdroje v Azure). 

+ Indexery vyžadují index vyhledávání. Indexer vyžaduje, abyste zadali schéma indexu, a to i v případě, že ho nikdy neplánujete použít. Minimální index obsahuje jedno pole řetězce určené jako klíč.

+ Indexery poskytují volitelná mapování polí, která se používají pro vytvoření aliasu zdrojového pole do cílového pole. Pokud mapování výchozích polí vyžaduje změnu (Pokud chcete použít jiný název nebo typ), můžete v indexeru vytvořit [mapování polí](search-indexer-field-mappings.md) . Pro výstup ve znalostní bázi Store může být cílem pole v objektu BLOB nebo v tabulce.

+ Indexery mají plány a další vlastnosti, jako jsou například mechanismy detekce změn poskytované různými zdroji dat, lze také použít pro znalostní bázi Knowledge Store. Například můžete [naplánovat](search-howto-schedule-indexers.md) rozšíření v pravidelných intervalech, abyste mohli aktualizovat obsah. 

## <a name="how-to-create-a-knowledge-store"></a>Postup vytvoření úložiště znalostí

K vytvoření úložiště Knowledge Store použijte portál nebo verzi Preview REST API (`api-version=2019-05-06-Preview`).

### <a name="use-the-azure-portal"></a>Použití portálu Azure

Průvodce **importem dat** obsahuje možnosti pro vytvoření úložiště znalostí. V případě prvotního průzkumu [Vytvořte své první znalostní báze ve čtyřech krocích](knowledge-store-connect-power-bi.md).

1. Vyberte podporovaný zdroj dat.

1. Zadat rozšíření: připojit prostředek, vybrat dovednosti a zadat úložiště znalostí. 

1. Vytvořte schéma indexu. Průvodce ho vyžaduje a může pro vás odvodit.

1. Spusťte průvodce. V tomto posledním kroku dojde k extrakci, obohacení a ukládání.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Použijte Create dovednosti a Preview REST API

`knowledgeStore` je definována v rámci objektu [dovednosti](cognitive-search-working-with-skillsets.md), který je zase vyvolán [indexerem](search-indexer-overview.md). Během obohacení Azure Kognitivní hledání ve vašem účtu Azure Storage vytvoří prostor a projekty obohacených dokumentů jako objekty blob nebo do tabulek v závislosti na vaší konfiguraci.

V současné době je náhled REST API jediným mechanismem, pomocí kterého můžete vytvořit úložiště znalostí programově. Snadným způsobem, jak prozkoumat, je [vytvořit první znalostní bázi Knowledge Store pomocí post a REST API](knowledge-store-create-rest.md).

Referenční obsah pro tuto funkci ve verzi Preview najdete v části [Reference k rozhraní API](#kstore-rest-api) tohoto článku. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Jak se připojit pomocí nástrojů a aplikací

Jakmile rozšíření existují v úložišti, můžete použít jakýkoli nástroj nebo technologii, která se připojuje ke službě Azure Blob nebo Table Storage, k prozkoumávání, analýze nebo využívání obsahu. Následující seznam je začátek:

+ [Průzkumník služby Storage](knowledge-store-view-storage-explorer.md) k zobrazení obohacené struktury dokumentu a obsahu. Vezměte ho jako základní nástroj pro zobrazení obsahu znalostní databáze.

+ [Power BI](knowledge-store-connect-power-bi.md) pro vytváření sestav a analýzy. 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) pro další manipulaci.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>Referenční materiály k rozhraním API

Tato část je verze referenčního dokumentu [Create dovednosti (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-skillset) , která se změnila tak, aby zahrnovala `knowledgeStore` definici. 

### <a name="example---knowledgestore-embedded-in-a-skillset"></a>Příklad – knowledgeStore vložený do dovednosti

Následující příklad ukazuje `knowledgeStore` v dolní části definice dovednosti. 

* Pro formulaci žádosti použijte **post** nebo **Put** .
* K získání přístupu k funkcím úložiště znalostí použijte `api-version=2019-05-06-Preview` verzi REST API. 

```http
POST https://[servicename].search.windows.net/skillsets?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```

Text požadavku je dokument JSON, který definuje dovednosti, který obsahuje `knowledgeStore`.

```json
{
  "name": "my-skillset-name",
  "description": "Extract organization entities and generate a positive-negative sentiment score from each document.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<YOUR-COGNITIVE-SERVICES-KEY>"
    },
    "knowledgeStore": { 
        "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
        "projections": [ 
            { 
                "tables": [  
                { "tableName": "Organizations", "generatedKeyName": "OrganizationId", "source": "/document/organizations*"}, 
                { "tableName": "Sentiment", "generatedKeyName": "SentimentId", "source": "/document/mySentiment"}
                ], 
                "objects": [ ], 
                "files": [  ]       
            }    
        ]     
    } 
}
```

### <a name="request-body-syntax"></a>Syntaxe textu žádosti  

Následující JSON určuje `knowledgeStore`, který je součástí [`skillset`](https://docs.microsoft.com/rest/api/searchservice/create-skillset), který je vyvolán `indexer` (nezobrazený). Pokud jste již obeznámeni s rozšířením AI, určuje dovednosti složení obohaceného dokumentu. Dovednosti musí obsahovat alespoň jednu dovednost, pravděpodobně Shaperou dovednost, pokud pracujete se strukturou dat.

Syntaxe pro strukturování datové části požadavku je následující.

```json
{   
    "name" : "Required for POST, optional for PUT requests which sets the name on the URI",  
    "description" : "Optional. Anything you want, or null",  
    "skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",
    "cognitiveServices": "A key to Cognitive Services, used for billing.",
    "knowledgeStore": { 
        "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
        "projections": [ 
            { 
                "tables": [ 
                    { "tableName": "<NAME>", "generatedKeyName": "<FIELD-NAME>", "source": "<DOCUMENT-PATH>" },
                    { "tableName": "<NAME>", "generatedKeyName": "<FIELD-NAME>", "source": "<DOCUMENT-PATH>" },
                    . . .
                ], 
                "objects": [ 
                    {
                    "storageContainer": "<BLOB-CONTAINER-NAME>", 
                    "source": "<DOCUMENT-PATH>", 
                    }
                ], 
                "files": [ 
                    {
                    "storageContainer": "<BLOB-CONTAINER-NAME>",
                    "source": "/document/normalized_images/*"
                    }
                ]  
            },
            {
                "tables": [ ],
                "objects": [ ],
                "files":  [ ]
            }  
        ]     
    } 
}
```

`knowledgeStore` má dvě vlastnosti: `storageConnectionString` pro Azure Storage účet a `projections` definující fyzické úložiště. Můžete použít libovolný účet úložiště, ale je cenově výhodné použít služby ve stejné oblasti.

Kolekce `projections` obsahuje objekty projekce. Každý objekt projekce musí mít `tables`, `objects``files` (jedna z nich), které jsou buď zadány, nebo null. Syntaxe uvedená výše ukazuje dva objekty, jeden úplně zadaný a druhý plně null. V rámci objektu projekce, jakmile je vyjádřen v úložišti, jsou zachovány všechny relace mezi daty, pokud byly zjištěny. 

Vytvořte tolik objektů projekce, kolik potřebujete k podpoře izolaci a specifických scénářů (například datové struktury používané pro zkoumání, oproti tomu, které jsou potřeba v rámci úlohy datové vědy). Můžete získat izolaci a přizpůsobení pro konkrétní scénáře nastavením `source` a `storageContainer` nebo `table` na různé hodnoty v rámci objektu. Další informace a příklady najdete v tématu [práce s projekcemi ve znalostní bázi Knowledge Store](knowledge-store-projection-overview.md).

|Vlastnost      | Platí pro | Popis|  
|--------------|------------|------------|  
|`storageConnectionString`| `knowledgeStore` | Povinná hodnota. V tomto formátu: `DefaultEndpointsProtocol=https;AccountName=<ACCOUNT-NAME>;AccountKey=<ACCOUNT-KEY>;EndpointSuffix=core.windows.net`|  
|`projections`| `knowledgeStore` | Povinná hodnota. Kolekce objektů vlastností sestávající z `tables`, `objects`, `files` a jejich odpovídajících vlastností. Nepoužívané projekce lze nastavit na hodnotu null.|  
|`source`| Všechny projekce| Cesta k uzlu stromu obohacení, který je kořenem projekce. Tento uzel je výstupem jakékoli dovednosti v dovednosti. Cesty začínají `/document/`, představující obohacený dokument, ale lze ho rozšířit na `/document/content/` nebo na uzly v rámci stromu dokumentu. Příklady: `/document/countries/*` (všechny země) nebo `/document/countries/*/states/*` (všechny stavy ve všech zemích). Další informace o cestách k dokumentům najdete v tématu [Koncepty a kompozice dovednosti](cognitive-search-working-with-skillsets.md).|
|`tableName`| `tables`| Tabulka, která se má vytvořit v úložišti tabulek v Azure |
|`storageContainer`| `objects`, `files`| Název kontejneru, který se má vytvořit ve službě Azure Blob Storage. |
|`generatedKeyName`| `tables`| Sloupec vytvořený v tabulce, který jednoznačně identifikuje dokument. Kanál pro obohacení vyplní tento sloupec generovanými hodnotami.|


### <a name="response"></a>Odpověď  

 V případě úspěšné žádosti by se měl zobrazit stavový kód "201 vytvořeno". Ve výchozím nastavení text odpovědi bude obsahovat JSON pro definici dovednosti, která byla vytvořena. Vyvolejte, že znalostní databáze není vytvořená, dokud nevrátíte indexer, který na tento dovednosti odkazuje.

## <a name="next-steps"></a>Další kroky

Znalostní báze Knowledge Store nabízí persistenci obohacených dokumentů, které jsou užitečné při navrhování dovednosti nebo vytváření nových struktur a obsahu pro použití všemi klientskými aplikacemi, které mají přístup k Azure Storagemu účtu.

Nejjednodušším přístupem k vytváření obohacených dokumentů je [portál](knowledge-store-create-portal.md), ale můžete také použít post a REST API, což je užitečnější, pokud chcete získat přehled o způsobu vytváření a odkazů na objekty.

> [!div class="nextstepaction"]
> [Vytvoření úložiště znalostí pomocí post a REST](knowledge-store-create-rest.md)
