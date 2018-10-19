---
title: Indexování objektů BLOB JSON pomocí indexeru Azure Search blob
description: Indexování objektů BLOB JSON pomocí indexeru Azure Search blob
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.openlocfilehash: a4689093508c3287e60da9d4668393e71211fbdd
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405698"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexování objektů BLOB JSON pomocí indexeru Azure Search blob
V tomto článku se dozvíte, jak nakonfigurovat indexer Azure Search blob k extrakci Strukturovaný obsah z objektů BLOB JSON ve službě Azure Blob storage.

Objekty BLOB JSON ve službě Azure Blob storage jsou obvykle jednotlivý dokument JSON nebo pole JSON. Indexování objektů blob ve službě Azure Search můžete analyzovat buď konstrukce, v závislosti na tom, jak nastavit **parsingMode** parametru na požadavek.

| Dokument JSON | parsingMode | Popis | Dostupnost |
|--------------|-------------|--------------|--------------|
| Jeden objekt blob | `json` | Analyzuje objektů BLOB JSON jako jediný neodkazovaný blok textu. Každý objekt blob JSON se změní na jeden dokument Azure Search. | Obecně dostupná v obou [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) a [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) rozhraní API. |
| Více na jeden objekt blob | `jsonArray` | Analyzuje pole JSON v objektu blob, kde každý prvek pole se změní na samostatné dokument Azure Search.  | Ve verzi preview v [REST api-version =`2017-11-11-Preview` ](search-api-2017-11-11-preview.md) a [.NET SDK ve verzi Preview](https://aka.ms/search-sdk-preview). |

> [!Note]
> Rozhraní API ve verzi Preview jsou určené pro testování a hodnocení a neměli byste používat v produkčním prostředí.
>

## <a name="setting-up-json-indexing"></a>Nastavení JSON indexování
Indexování objektů BLOB JSON je podobný extrakce běžný dokument v pracovním postupu třemi částmi společné pro všechny indexery ve službě Azure Search.

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

Prvním krokem je zadání zdroje informací o připojení dat používaný indexerem. Zadaný typ zdroje dat jako `azureblob`, určuje, jaké chování extrakce dat jsou vyvolány pomocí indexeru. Indexování objektů blob JSON, definice zdroje dat je stejný pro dokumenty JSON a pole. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>Krok 2: Vytvoření cílovým indexem vyhledávání 

Indexery jsou párovány s schématu indexu. Pokud používáte rozhraní API (a ne na portálu), připravte indexu předem tak, aby jej zadat v operaci indexeru. 

> [!Note]
> Indexery jsou přístupné na portálu, až **Import** akce pro omezený počet všeobecně dostupné indexery. Pracovní postup importu často, můžete vytvořit často předběžný index na základě metadat ve zdroji. Další informace najdete v tématu [Import dat do Azure Search na portálu](search-import-data-portal.md).

### <a name="step-3-configure-and-run-the-indexer"></a>Krok 3: Konfigurace a spuštění indexeru

Až doteď byly definice pro zdroj dat a indexu parsingMode nezávislé. V kroku 3 do konfigurace indexeru, ale cesta diverges v závislosti na tom, jak chcete objektů blob JSON obsah analyzovat a strukturované v indexu Azure Search.

Při volání metody indexeru, postupujte takto:

+ Nastavte **parsingMode** parametr `json` (k indexování jednotlivých objektů blob jako jeden dokument) nebo `jsonArray` (Pokud objektů BLOB obsahovat pole JSON a je třeba každý prvek pole považován za samostatný dokumentu).

+ Volitelně můžete pomocí **mapování polí** zvolte Vlastnosti ve zdrojovém dokumentu JSON, které se používají k naplnění cílovým indexem vyhledávání. Pro pole JSON pole existuje jako vlastnost pro nižší úrovně, můžete nastavit kořen dokumentu určující umístění pole v rámci objektu blob.

> [!IMPORTANT]
> Při použití `json` nebo `jsonArray` režim parsování Azure Search se předpokládá, že všechny objekty BLOB ve zdroji dat obsahovat JSON. Pokud potřebujete podporovat kombinaci objekty BLOB JSON a jiné JSON do stejného zdroje dat, dejte nám vědět o [náš web UserVoice](https://feedback.azure.com/forums/263029-azure-search).


## <a name="how-to-parse-single-json-blobs"></a>Jak analyzovat jeden objektů BLOB JSON

Ve výchozím nastavení [indexeru Azure Search blob](search-howto-indexing-azure-blob-storage.md) analyzuje objektů BLOB JSON jako jediný neodkazovaný blok textu. Často chcete zachovat strukturu dokumentů JSON. Předpokládejme například, že máte následující dokument JSON ve službě Azure Blob storage:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>Definice indexeru pro jeden objektů BLOB JSON

Pomocí indexeru Azure Search blob, je dokument JSON podobný jako předchozí příklad analyzován do jednoho dokumentu Azure Search. Indexer načte index to provede spárováním odpovídajících "text", "datePublished" a "značek" ze zdroje proti identicky názvem a typem cílového pole.

Konfigurace je součástí těla operace indexeru. Připomínáme, že objekt zdroje dat, definovali dříve, určuje informace datového zdroje typu a připojení. Kromě toho cílový index musí existovat také jako prázdného zásobníku ve své službě. Plán a parametry jsou volitelné, ale pokud je vynecháte, spuštění indexeru okamžitě, pomocí `json` analýzy režim.

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

Jak je uvedeno, nejsou nutné mapování polí. Zadaný index s "text", "datePublished a"značek"polí, objekt blob indexer lze odvodit správné mapování bez pole mapování v požadavku.

## <a name="how-to-parse-json-arrays-preview"></a>Jak parsovat JSON pole (preview)

Alternativně se můžete rozhodnout pro funkce ve verzi preview pole JSON. Tato možnost je užitečná, pokud objekty BLOB obsahují *pole objektů JSON*, a chcete, aby každý prvek stane samostatnou dokumentů Azure Search. Například s ohledem následujících objektů blob JSON, která můžete naplnit index Azure Search pomocí tří samostatných dokumentů, každý s pole "id" a "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>Definice indexeru pro pole JSON

Pro pole JSON, indexer požadavek používá rozhraní API ve verzi preview a `jsonArray` analyzátor. Jedná se pouze dva požadavky pole specifické pro indexování objektů BLOB JSON.

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

Opět si všimněte, že mapování polí se nevyžadují. Zadaný index pole "id" a "text", indexeru blob odvodit správné mapování bez seznamu mapování pole.

<a name="nested-json-arrays"></a>

### <a name="nested-json-arrays"></a>Vnořená pole JSON
Co když chcete indexu pole objektů JSON, ale toto pole je vnořená někde v rámci dokumentu? Můžete si vybrat vlastností, které obsahuje pole pomocí `documentRoot` vlastnost konfigurace. Pokud například objektů BLOB vypadat nějak takto:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Použití této konfigurace k indexování pole obsažené ve `level2` vlastnost:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="using-field-mappings-to-build-search-documents"></a>Použití mapování polí k sestavení vyhledávání dokumentů

Při zdrojové a cílové pole nejsou zarovnány dokonale, můžete definovat části mapování pole v textu požadavku pro explicitní přiřazení jednoho pole.

V současné době Azure Search nemůže indexovat libovolné dokumenty JSON přímo, protože podporuje pouze primitivní datové typy, pole řetězce a GeoJSON body. Můžete však použít **mapování polí** "přenést" je do nejvyšší úrovně pole hledání v dokumentech a výběr součástí dokumentu JSON. Další informace o základní informace o mapování polí najdete v tématu [mapování polí v indexerech Azure Search](search-indexer-field-mappings.md).

Byste nepřešli znovu na náš příklad dokumentu JSON:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Předpokládejme indexu vyhledávání u následujících polí: `text` typu `Edm.String`, `date` typu `Edm.DateTimeOffset`, a `tags` typu `Collection(Edm.String)`. Všimněte si, že rozdíl mezi "datePublished" ve zdroji a `date` pole v indexu. Pokud chcete namapovat vaše struktury JSON do požadované podoby, použijte následující mapování polí:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Názvy polí zdroje v mapování jsou určeny pomocí [ukazatel JSON](http://tools.ietf.org/html/rfc6901) zápis. Začínat lomítkem k odkazování na kořen dokumentu JSON a pak vyberte požadovanou vlastnost (na libovolné úrovni vnoření) pomocí dopředného cesty oddělených lomítkem.

Jednotlivá pole prvků lze také odkazovat pomocí index založený na nule. Například vyberte první prvek pole "tags" v příkladu výše, použijte pole mapování následujícím způsobem:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Pokud název zdrojového pole v cestě mapování pole odkazuje na vlastnost, která neexistuje ve formátu JSON, toto mapování se přeskočí bez chyby. To se provádí tak, aby podporujeme dokumenty s jiným schématem (což je běžným případem použití). Protože neexistuje žádné ověření, budete muset snažte se vyhnout překlepy specifikací mapování pole.
>
>

## <a name="example-indexer-request-with-field-mappings"></a>Příklad: Požadavek Indexer s mapování polí

V následujícím příkladu je plně zadaný indexer datovou část, včetně mapování polí:

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
Pokud máte požadavky na funkce nebo nápady na vylepšení, kontaktujte nás na naše [webu UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="see-also"></a>Další informace najdete v tématech

+ [Indexery ve službě Azure Search](search-indexer-overview.md)
+ [Indexování služby Azure Blob Storage pomocí služby Azure Search](search-howto-index-json-blobs.md)
+ [Indexování objektů BLOB CSV pomocí indexeru Azure Search blob](search-howto-index-csv-blobs.md)
+ [Kurz: Prohledávání částečně strukturovaných dat z úložiště objektů Blob v Azure ](search-semi-structured-data.md)
