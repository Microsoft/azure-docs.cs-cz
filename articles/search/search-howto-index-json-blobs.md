---
title: Indexování objekty BLOB JSON s indexer objektu blob Azure Search
description: Indexování objekty BLOB JSON s indexer objektu blob Azure Search
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: 752df29200a5e020ccf10f511ae2f02c0d72bd48
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362998"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexování objekty BLOB JSON s indexer objektu blob Azure Search
Tento článek ukazuje, jak nakonfigurovat indexer Azure Search objektů blob k extrahování strukturovaných obsahu z JSON objektů BLOB v Azure Blob storage.

Objekty BLOB JSON v Azure Blob storage jsou obvykle jednotlivý dokument JSON nebo pole JSON. Indexer objektů blob ve službě Azure Search můžete analyzovat buď konstrukce, v závislosti na tom, jak nastavit **parsingMode** parametru na požadavek.

| Dokument JSON | parsingMode | Popis | Dostupnost |
|--------------|-------------|--------------|--------------|
| Jeden na každý objekt blob | `json` | Analyzuje objekty BLOB JSON jako jeden blok textu. Každý objekt JSON blob se změní na jeden dokument Azure Search. | Obecně k dispozici v obou [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) a [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) rozhraní API. |
| Více na jeden objekt blob | `jsonArray` | Analyzuje pole JSON do objektu BLOB, kde každý element pole se změní na samostatné dokument Azure Search.  | Ve verzi preview v [REST api-version =`2017-11-11-Preview` ](search-api-2017-11-11-preview.md) a [.NET SDK Preview](https://aka.ms/search-sdk-preview). |

> [!Note]
> Rozhraní API Preview jsou určené pro testování a vyhodnocení a nesmí být použita v produkčním prostředí.
>

## <a name="setting-up-json-indexing"></a>Nastavení indexování JSON
Indexování objekty BLOB JSON je podobná extrakce běžný dokument v pracovním postupu třemi částmi společné pro všechny indexery ve službě Azure Search.

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

Prvním krokem je k poskytování informací připojení zdroje dat používané indexeru. Zadaný typ zdroje dat sem jako `azureblob`, určuje, jaké chování extrakce dat jsou vyvolány indexeru. Pro objekt blob JSON indexování zdroj dat je, že definice je stejný pro dokumenty JSON a pole. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>Krok 2: Vytvoření cílový index vyhledávání 

Indexery jsou spárována s schématu indexu. Pokud používáte rozhraní API (nikoli portálu), připravte indexu předem, aby zadejte jej v operaci indexer. 

> [!Note]
> Indexery jsou přístupné na portálu prostřednictvím **Import** akce pro omezený počet všeobecně dostupná indexery. Pracovní postup importu často, můžete vytvořit často předběžné index na základě metadat ve zdroji. Další informace najdete v tématu [importovat data do Azure Search na portálu](search-import-data-portal.md).

### <a name="step-3-configure-and-run-the-indexer"></a>Krok 3: Konfigurace a spuštění indexeru

Dosud se definice pro zdroj dat a index bez ohledu na parsingMode. V kroku 3 pro Indexer konfiguraci, ale cesta diverges v závislosti na tom, jak chcete JSON blob obsah analyzovat a strukturovaná v indexu Azure Search.

Při volání metody indexeru, postupujte takto:

+ Nastavte **parsingMode** parametru `json` (do indexu každý objekt blob jako jedním dokumentem) nebo `jsonArray` (Pokud objektů BLOB obsahovat pole JSON a je třeba každý element pole jsou považovány za samostatný dokument).

+ Volitelně můžete pomocí **pole mapování** zvolit, které vlastnosti Zdrojový dokument JSON slouží k naplnění indexu vyhledávání cíl. Pro pole JSON pokud existuje pole jako nižší úrovně vlastnost můžete nastavit kořen dokumentu, která určuje, kde je umístěn pole v objektu blob.

> [!IMPORTANT]
> Při použití `json` nebo `jsonArray` analýzy režimu Azure Search předpokládá, že všechny objekty BLOB ve zdroji dat obsahovat JSON. Pokud potřebujete podporovat směs JSON a jiný JSON objekty BLOB ve stejném datovém zdroji, dejte nám vědět na [našeho webu UserVoice](https://feedback.azure.com/forums/263029-azure-search).


## <a name="how-to-parse-single-json-blobs"></a>Jak analyzovat jeden objekty BLOB JSON

Ve výchozím nastavení [indexer objektu blob Azure Search](search-howto-indexing-azure-blob-storage.md) analyzuje objekty BLOB JSON jako jeden blok textu. Často budete chtít zachovat strukturu dokumentů JSON. Například předpokládejme, že máte následující dokumentu JSON v Azure Blob storage:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>Definice indexeru pro jeden objekty BLOB JSON

Použití indexeru pro hledání Azure blob, podobně jako v předchozím příkladu dokumentu JSON je analyzována do jednoho dokumentu Azure Search. Indexer načte index pomocí odpovídajících "text", "datePublished" a "značky" ze zdroje proti stejně jako s názvem a typem cílová pole.

Konfigurace je k dispozici v těle operace indexer. Odvolat, že objekt zdroje dat, dříve definovány, určuje informace zdroje dat typu a připojení. Kromě toho cílový index musí existovat také jako prázdný kontejner ve službě. Plán a parametry jsou volitelné, ale pokud je vynecháte, spuštění indexeru okamžitě, pomocí `json` analýzy režim.

Plně zadaný požadavek může vypadat takto:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Jak jsme uvedli, nejsou nutné mapování polí. Zadaný index s "text", "datePublished a"značky"polí, objekt blob indexer může odvodit správnou mapování bez pole mapování přítomné v žádosti.

## <a name="how-to-parse-json-arrays-preview"></a>Jak analyzovat pole JSON (preview)

Případně se můžete rozhodnout pro funkci preview pole JSON. Tato možnost je užitečná, pokud objekty BLOB obsahují *pole objektů JSON*, a chcete, aby každý prvek se samostatný dokument Azure Search. Například následující JSON blob zadána jej můžete naplnit index Azure Search s tři samostatné dokumenty, každý s pole "id" a "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>Indexer definice pole JSON

Pro pole JSON, žádost indexer používá rozhraní API ve verzi preview a `jsonArray` analyzátor. Toto jsou požadavky na pouze dvě pole specifické pro indexování objekty BLOB JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Znovu Všimněte si, že mapování polí nejsou nutné. Zadaný index s pole "id" a "text", objektů blob indexeru odvození správné mapování bez seznam mapování polí.

<a name="nested-json-arrays"></a>

### <a name="nested-json-arrays"></a>Vnořená pole JSON
Co dělat, když chcete indexu pole objektů JSON, ale tohoto pole je vnořený někde v tomto dokumentu? Můžete si vybrat vlastností, které obsahuje pole pomocí `documentRoot` vlastnosti konfigurace. Pokud například objektů BLOB vypadat takto:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Pomocí této konfigurace můžete indexu pole obsažené ve `level2` vlastnost:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="using-field-mappings-to-build-search-documents"></a>Použití mapování polí k sestavení vyhledávání dokumentů

Pokud zdrojové a cílové pole nejsou zarovnány perfektně, můžete definovat oddíl mapování pole v textu požadavku pro explicitní přidružení jednoho pole.

V současné době Azure Search nemůže indexovat libovolné dokumenty JSON přímo, protože podporuje jenom primitivní datové typy, řetězce pole a GeoJSON body. Můžete však použít **pole mapování** a vyberte části dokumentu JSON "navýšení" je do nejvyšší úrovně pole hledání dokumentu. Další informace o základní informace o mapování pole najdete v tématu [pole mapování v Azure Search indexery](search-indexer-field-mappings.md).

Opětovné návštěvy dokumentu JSON náš příklad:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Předpokládejme index vyhledávání s následující pole: `text` typu `Edm.String`, `date` typu `Edm.DateTimeOffset`, a `tags` typu `Collection(Edm.String)`. Všimněte si rozporu "datePublished" ve zdroji a `date` pole v indexu. Chcete-li mapování vaše struktury JSON na požadovaný tvar, použijte následující mapování polí:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Názvy polí zdroje v mapování jsou určeny pomocí [JSON ukazatel](http://tools.ietf.org/html/rfc6901) zápis. Začínat lomítkem odkazovat na kořen dokumentu JSON a potom vyberte požadované vlastnosti (na libovolné úroveň vnoření) pomocí dopředného cesty oddělené lomítko.

Můžete také odkazovat na pole jednotlivé elementy pomocí index počítaný od nuly. Například vyberte první prvek pole "značky" z výše uvedeném příkladu, použijte mapování polí takto:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Pokud název pole zdroje v cestě mapování pole odkazuje na vlastnost, která neexistuje ve formátu JSON, že mapování přeskočen bez chyby. To se provádí tak, aby podporujeme dokumentů s zvolte jiné schéma (což je běžný případ použití). Protože není k dispozici žádné ověření, budete muset Ujistěte se, aby se zabránilo překlepům specifikací mapování pole.
>
>

## <a name="example-indexer-request-with-field-mappings"></a>Příklad: Indexer žádost s mapování polí

V následujícím příkladu je datové části plně zadaný indexer, včetně mapování polí:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


## <a name="help-us-make-azure-search-better"></a>Pomozte nám vylepšit Azure Search
Pokud máte žádosti o funkce nebo vylepšení nápady, oslovení nám na našem [UserVoice lokality](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="see-also"></a>Další informace najdete v tématech

+ [Indexery ve službě Azure Search](search-indexer-overview.md)
+ [Indexování úložiště objektů Blob v Azure s Azure Search](search-howto-index-json-blobs.md)
+ [Indexování CSV objekty BLOB se indexer objektu blob Azure Search](search-howto-index-csv-blobs.md)
+ [Kurz: Vyhledávání částečně strukturovaných dat z Azure Blob storage ](search-semi-structured-data.md)