---
title: Indexování objektů BLOB JSON z objektů Blob v Azure indexeru pro fulltextové vyhledávání – Azure Search
description: Procházení objektů BLOB Azure JSON pro textový obsah pomocí indexeru Azure Search Blob. Indexery můžete automatizovat příjem dat pro vybrané zdroje dat jako úložiště objektů Blob v Azure.
ms.date: 12/21/2018
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 6df8d9a5c1ca1e587834ea08f73b3dd9498f8537
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753145"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexování objektů BLOB JSON pomocí indexeru Azure Search Blob
V tomto článku se dozvíte, jak nakonfigurovat indexer Azure Search blob k extrakci Strukturovaný obsah z objektů BLOB JSON ve službě Azure Blob storage.

Můžete použít [portál](#json-indexer-portal), [rozhraní REST API](#json-indexer-rest), nebo [sady .NET SDK](#json-indexer-dotnet) do indexu obsah JSON. Společná pro všechny přístupy se nachází v kontejneru objektů blob v účtu služby Azure Storage dokumentů JSON. Pokyny k odesílání dokumentů JSON z jiných platforem než Azure najdete v tématu [import dat ve službě Azure Search](search-what-is-data-import.md).

Objekty BLOB JSON ve službě Azure Blob storage jsou obvykle jednotlivý dokument JSON nebo pole JSON. Indexování objektů blob ve službě Azure Search můžete analyzovat buď konstrukce, v závislosti na tom, jak nastavit **parsingMode** parametru na požadavek.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Použití portálu

Nejjednodušším způsobem pro indexování dokumentů JSON je použití Průvodce v [webu Azure portal](https://portal.azure.com/). Pomocí analýzy metadat v kontejneru objektů blob v Azure [ **importovat data** ](search-import-data-portal.md) průvodce můžete vytvořit výchozí index, mapují pole zdroje na cíl pole indexu a načtení indexu v rámci jedné operace. V závislosti na velikosti a složitosti zdroje dat může mít indexu provozní fulltextové vyhledávání v minutách.

### <a name="1---prepare-source-data"></a>1 – Příprava zdrojových dat

Měli byste účtu služby Azure storage, Blob storage a kontejner dokumentů JSON. Pokud nejste obeznámeni s kteroukoli z těchto úloh, kontrola požadovaných součástí "Vytvoření objektů Blob v Azure služby a načtení ukázkových dat" v [kognitivní vyhledávání quickstart](cognitive-search-quickstart-blob.md#set-up-azure-blob-service-and-load-sample-data).

### <a name="2---start-import-data-wizard"></a>2 – Spusťte Průvodce importem dat

Je možné [spusťte průvodce](search-import-data-portal.md) na panelu příkazů na stránce služby Azure Search, nebo kliknutím **přidat Azure Search** v **službu Blob service** části vašeho účtu úložiště levé navigační podokno.

### <a name="3---set-the-data-source"></a>3 – nastavení zdroje dat

V **zdroj dat** stránku, musí být zdroj **Azure Blob Storage**, s následujícími specifikacemi:

+ **Data k extrakci** by měl být *obsah a metadata*. Pokud vyberete tuto možnost umožňuje Průvodce odvodit schéma indexu a mapování polí pro import.
   
+ **Režim parsování** by mělo být nastavené *JSON* nebo *pole JSON*. 

  *JSON* zabývá se výhodami jednotlivých objektů blob jako dokument hledání jednoduchého, objeví jako nezávislou položku ve výsledcích hledání. 

  *Pole JSON* je pro objekty BLOB skládá z několika prvků, kde má každý prvek na kloubové jako samostatná, nezávislé hledání v dokumentech. Pokud objekty BLOB jsou komplexní a nevyberete *pole JSON* celý objekt blob se ingestuje jako jeden dokument.
   
+ **Kontejner úložiště** musíte zadat svůj účet úložiště a kontejner nebo připojovací řetězec, který se přeloží do kontejneru. Na stránce portálu služby objektů Blob můžete získat připojovací řetězce.

   ![Definice zdroje dat objektu BLOB](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 – přeskočte stránku "Přidat kognitivní vyhledávání" v Průvodci

Přidat kognitivní dovednosti není nutné pro import dokumentu JSON. Pokud nemáte specifickou potřebu [patří rozhraní API služeb Cognitive Services a transformace](cognitive-search-concept-intro.md) na váš kanál indexování by měl tento krok přeskočit.

Pokud chcete přeskočit krok, klikněte na další stránku **přizpůsobit cílový index**.

### <a name="5---set-index-attributes"></a>5 - atributy indexu set

V **Index** stránky, zobrazí se seznam polí s typem dat a řadu zaškrtávací políčka pro nastavení atributy indexu. Průvodce může vygenerovat výchozí index založený na metadata a vzorkováním zdrojová data. 

Výchozí hodnoty často vytvořit funkční řešení: výběrem pole (přetypování jako řetězec) a slouží jako klíč dokumentu ID k jedinečné identifikaci každého dokumentu a také pole, které jsou vhodnými kandidáty pro fulltextové vyhledávání a retrievable do sady výsledků dotazu. Pro objekty BLOB `content` pole je nejlepší kandidát pro prohledávatelný obsah.

Můžete přijmout výchozí hodnoty nebo si prostudujte popis, [atributy indexu](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) a [jazykové analyzátory](https://docs.microsoft.com/rest/api/searchservice/language-support) přepsat nebo doplnit počáteční hodnoty. 

Za chvíli zkontrolujte zvolené položky. Po spuštění Průvodce fyzické datové struktury jsou vytvořeny a nebudou moct tato pole upravovat bez vyřadit a znovu vytvořit všechny objekty.

   ![Definice indexu objektů BLOB](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 – Vytvoření indexeru

Plně zadaný, Průvodce vytvoří tři různé objekty ve vyhledávací službě. Objekt zdroje dat a indexu objektu se ukládají jako pojmenovaným prostředkům ve službě Azure Search. Poslední krok vytvoří objekt indexeru. Pojmenování indexer umožňuje existuje jako samostatný prostředek, který můžete naplánovat a spravovat bez ohledu na jejich rejstřík a data zdrojový objekt, vytvoří ve stejném pořadí průvodce.

Pokud nejste obeznámeni s indexery, *indexer* je prostředek ve službě Azure Search, která prochází externího zdroje dat pro prohledávatelný obsah. Výstup **importovat data** Průvodce indexer je výsledkem, který prochází zdroje dat JSON, extrahuje prohledávatelný obsah a naimportuje do indexu Azure Search.

   ![Definice indexeru BLOB](media/search-howto-index-json/import-wizard-json-indexer.png)

Klikněte na tlačítko **OK** ke spuštění průvodce a vytvoření všech objektů. Indexování začíná okamžitě.

Můžete monitorovat import dat do stránky portálu. Oznámení o průběhu označuje stav indexování a kolik dokumenty jsou odeslány. Při indexování hotový, můžete použít [Průzkumníka služby Search](search-explorer.md) k dotazování indexu.

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Použití rozhraní REST API

Indexování objektů BLOB JSON je podobný extrakce běžný dokument v pracovním postupu třemi částmi společné pro všechny indexery ve službě Azure Search: vytvoření zdroje dat, vytvoření indexu, vytvořením indexeru.

Založený na kódu JSON indexování, můžete použít rozhraní REST API s rozhraními API pro [indexery](https://docs.microsoft.com/rest/api/searchservice/create-indexer), [zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source), a [indexy](https://docs.microsoft.com/rest/api/searchservice/create-index). Rozdíl od průvodce portálem, vyžaduje přístup založený na kódu, abyste měli indexu na místě a připravená přijímat dokumenty JSON, když posíláte **vytvoření indexeru** požadavku.

Objekty BLOB JSON ve službě Azure Blob storage jsou obvykle jednotlivý dokument JSON nebo pole JSON. Indexování objektů blob ve službě Azure Search můžete analyzovat buď konstrukce, v závislosti na tom, jak nastavit **parsingMode** parametru na požadavek.

| Dokument JSON | parsingMode | Popis | Dostupnost |
|--------------|-------------|--------------|--------------|
| Jeden objekt blob | `json` | Analyzuje objektů BLOB JSON jako jediný neodkazovaný blok textu. Každý objekt blob JSON se změní na jeden dokument Azure Search. | Obecně dostupná v obou [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) a [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) rozhraní API. |
| Více na jeden objekt blob | `jsonArray` | Analyzuje pole JSON v objektu blob, kde každý prvek pole se změní na samostatné dokument Azure Search.  | Obecně dostupná v obou [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) a [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) rozhraní API. |


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

Index ukládá prohledávatelný obsah ve službě Azure Search. K vytvoření indexu, zadejte schéma, které určuje pole v dokumentu, atributy a jiných objektů, které obrazce vyhledávání. Pokud vytvoříte index, který má stejné názvy polí a datové typy jako zdroj, indexer bude odpovídat pole zdrojové a cílové uložení práce by bylo nutné explicitně mapování polí.

Následující příklad ukazuje [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) požadavku. Index bude mít možností prohledávání `content` pole, které chcete uložit text extrahovaný z objektů blob:   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="step-3-configure-and-run-the-indexer"></a>Krok 3: Konfigurace a spuštění indexeru

Až doteď byly definice pro zdroj dat a indexu parsingMode nezávislé. V kroku 3 do konfigurace indexeru, ale cesta diverges v závislosti na tom, jak chcete objektů blob JSON obsah analyzovat a strukturované v indexu Azure Search. Možnosti jsou `json` nebo `jsonArray`:

+ Nastavte **parsingMode** k `json` indexování jednotlivých objektů blob jako jeden dokument.

+ Nastavte **parsingMode** k `jsonArray` Pokud objektů blob se skládají z pole JSON, a je třeba každý prvek pole se samostatnou dokumentu ve službě Azure Search. Dokument si lze představit jako jedinou položku ve výsledcích hledání. Pokud chcete, aby každý prvek v poli zobrazení ve výsledcích hledání jako nezávislou položku, použijte `jsonArray` možnost.

V rámci definice indexeru můžete volitelně použít **mapování polí** zvolte Vlastnosti ve zdrojovém dokumentu JSON, které se používají k naplnění cílovým indexem vyhledávání. Pro pole JSON pole existuje jako vlastnost nižší úrovně, můžete nastavit kořen dokumentu určující umístění pole v rámci objektu blob.

> [!IMPORTANT]
> Při použití `json` nebo `jsonArray` režim parsování Azure Search se předpokládá, že všechny objekty BLOB ve zdroji dat obsahovat JSON. Pokud potřebujete podporovat kombinaci objekty BLOB JSON a jiné JSON do stejného zdroje dat, dejte nám vědět o [náš web UserVoice](https://feedback.azure.com/forums/263029-azure-search).


### <a name="how-to-parse-single-json-blobs"></a>Jak analyzovat jeden objektů BLOB JSON

Ve výchozím nastavení [indexeru Azure Search blob](search-howto-indexing-azure-blob-storage.md) analyzuje objektů BLOB JSON jako jediný neodkazovaný blok textu. Často chcete zachovat strukturu dokumentů JSON. Předpokládejme například, že máte následující dokument JSON ve službě Azure Blob storage:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Indexování objektů blob analyzuje dokument JSON do jednoho dokumentu Azure Search. Indexer načte index to provede spárováním odpovídajících "text", "datePublished" a "značek" ze zdroje proti cílové identicky názvem a typem indexu pole.

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

### <a name="how-to-parse-json-arrays"></a>Jak analyzovat pole JSON

Alternativně se můžete rozhodnout pro funkci pole JSON. Tato možnost je užitečná, pokud objekty BLOB obsahují *pole objektů JSON*, a chcete, aby každý prvek stane samostatnou dokumentů Azure Search. Například s ohledem následujících objektů blob JSON, která můžete naplnit index Azure Search pomocí tří samostatných dokumentů, každý s pole "id" a "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Pro pole JSON by měla vypadat podobně jako v následujícím příkladu definice indexeru. Všimněte si, že parsingMode parametr určuje, `jsonArray` analyzátor. Zadáním analyzátor správný a máte správná data jsou vstupní pouze dva požadavky pole specifické pro indexování objektů BLOB JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Opět si všimněte, že mapování polí lze vynechat. Za předpokladu, že index s identicky pojmenovanou "id" a "text" pole, indexeru blob odvodit správné mapování bez přehledu explicitní pole mapování.

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

### <a name="using-field-mappings-to-build-search-documents"></a>Použití mapování polí k sestavení vyhledávání dokumentů

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

Názvy polí zdroje v mapování jsou určeny pomocí [ukazatel JSON](https://tools.ietf.org/html/rfc6901) zápis. Začínat lomítkem k odkazování na kořen dokumentu JSON a pak vyberte požadovanou vlastnost (na libovolné úrovni vnoření) pomocí dopředného cesty oddělených lomítkem.

Jednotlivá pole prvků lze také odkazovat pomocí index založený na nule. Například vyberte první prvek pole "tags" v příkladu výše, použijte pole mapování následujícím způsobem:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Pokud název zdrojového pole v cestě mapování pole odkazuje na vlastnost, která neexistuje ve formátu JSON, toto mapování se přeskočí bez chyby. To se provádí tak, aby podporujeme dokumenty s jiným schématem (což je běžným případem použití). Protože neexistuje žádné ověření, budete muset snažte se vyhnout překlepy specifikací mapování pole.
>
>

### <a name="rest-example-indexer-request-with-field-mappings"></a>Příklad REST: Indexer požadavek s mapování polí

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

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Použití sady .NET SDK

Sady .NET SDK má plně parity pomocí rozhraní REST API. Doporučujeme, abyste si předchozí části rozhraní REST API, další koncepty, pracovních postupů a požadavků. Poté můžete odkázat na následující referenční dokumentace rozhraní API .NET k implementaci JSON indexer ve spravovaném kódu.

+ [Microsoft.Azure.Search.models.DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [Microsoft.Azure.Search.models.DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [Microsoft.Azure.Search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [Microsoft.Azure.Search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

## <a name="see-also"></a>Další informace najdete v tématech

+ [Indexery ve službě Azure Search](search-indexer-overview.md)
+ [Indexování služby Azure Blob Storage pomocí služby Azure Search](search-howto-index-json-blobs.md)
+ [Indexování objektů BLOB CSV pomocí indexeru Azure Search blob](search-howto-index-csv-blobs.md)
+ [Kurz: Prohledávání částečně strukturovaných dat z úložiště objektů Blob v Azure](search-semi-structured-data.md)
