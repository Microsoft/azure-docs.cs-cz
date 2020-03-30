---
title: Hledání přes objekty BLOB JSON
titleSuffix: Azure Cognitive Search
description: Procházení objektů BLOB Azure JSON pro textový obsah pomocí indexeru objektů blob azure cognitive search. Indexery automatizují ingestování dat pro vybrané zdroje dat, jako je úložiště objektů blob Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 37fc78971124240077a59d4ad99aa06cc408dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533971"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Jak indexovat objekty BLOB JSON pomocí indexeru objektů blob v Azure Cognitive Search

Tento článek ukazuje, jak nakonfigurovat [indexer](search-indexer-overview.md) objektů blob Azure Cognitive Search extrahovat strukturovaný obsah z dokumentů JSON v úložišti objektů blob Azure a učinit jej prohledávatelný v Azure Cognitive Search. Tento pracovní postup vytvoří index Azure Cognitive Search a načte ho existujícím textem extrahovaným z objektů BLOB JSON. 

K indexování obsahu JSON můžete použít [portál](#json-indexer-portal), [rozhraní REST API](#json-indexer-rest)nebo soubor [.NET SDK.](#json-indexer-dotnet) Společné pro všechny přístupy je, že dokumenty JSON jsou umístěny v kontejneru objektů blob v účtu Azure Storage. Pokyny k odesílání dokumentů JSON z jiných platforem mimo Azure najdete [v tématu Import dat v Azure Cognitive Search](search-what-is-data-import.md).

Objekty BLOB JSON v úložišti objektů blob Azure jsou obvykle buď `json`jeden dokument JSON (režim analýzy) nebo kolekce entit JSON. Pro kolekce objekt blob může mít **pole** dobře tvarované prvky JSON (režim analýzy je). `jsonArray` Objekty BLOB se také mohou skládat z více jednotlivých entit JSON oddělených novým řádkem (režim analýzy je). `jsonLines` Parametr **parsingMode** v požadavku určuje výstupní struktury.

> [!NOTE]
> Další informace o indexování více vyhledávacích dokumentů z jednoho objektu blob naleznete v [tématu Indexing 1:N](search-howto-index-one-to-many-blobs.md).

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Použití portálu

Nejjednodušší metodou pro indexování dokumentů JSON je použití průvodce na [webu Azure Portal](https://portal.azure.com/). Analýzou metadat v kontejneru objektů blob Azure může Průvodce [**importem dat**](search-import-data-portal.md) vytvořit výchozí index, namapovat zdrojová pole na cílová pole indexu a načíst index do jedné operace. V závislosti na velikosti a složitosti zdrojových dat můžete mít provozní fulltextový vyhledávací index během několika minut.

Doporučujeme používat stejnou oblast nebo umístění pro Azure Cognitive Search a Azure Storage pro nižší latenci a vyhnout se poplatkům za šířku pásma.

### <a name="1---prepare-source-data"></a>1 - Příprava zdrojových dat

[Přihlaste se k portálu Azure](https://portal.azure.com/) a [vytvořte kontejner objektů blob,](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) který bude obsahovat vaše data. Úroveň veřejného přístupu lze nastavit na libovolnou z jejích platných hodnot.

K načtení dat v Průvodci **importem dat** budete potřebovat název účtu úložiště, název kontejneru a přístupový klíč.

### <a name="2---start-import-data-wizard"></a>2 - Průvodce spuštěním importu dat

Na stránce Přehled vyhledávací služby můžete [průvodce spustit](search-import-data-portal.md) z panelu příkazů.

   ![Příkaz Importovat data na portálu](./media/search-import-data-portal/import-data-cmd2.png "Spuštění Průvodce importem dat")

### <a name="3---set-the-data-source"></a>3 - Nastavení zdroje dat

Na stránce **zdroje dat** musí být zdrojem **Azure Blob Storage**s následujícími specifikacemi:

+ **Data, která mají být extrahována,** by měla být *Obsah a metadata*. Výběrem této možnosti může průvodce odvodit schéma indexu a namapovat pole pro import.
   
+ **Režim analýzy** by měl být nastaven na řádky *JSON*, *JSON nebo* *JSON*. 

  *JSON* artikuluje každý objekt blob jako jeden dokument hledání, zobrazí se jako nezávislá položka ve výsledcích hledání. 

  *JSON pole* je pro objekty BLOB, které obsahují dobře formátovaná data JSON - dobře formátovaný JSON odpovídá poli objektů nebo má vlastnost, která je pole objektů a chcete, aby každý prvek byl formulován jako samostatný, nezávislý vyhledávací dokument. Pokud objekty BLOB jsou složité a nezvolíte *pole JSON,* celý objekt blob je ingestován jako jeden dokument.

  *Řádky JSON* je pro objekty BLOB složené z více entit JSON oddělených novým řádkem, kde chcete, aby každá entita byla formulována jako samostatný nezávislý vyhledávací dokument. Pokud objekty BLOB jsou složité a nezvolíte režim analýzy *řádků JSON,* bude celý objekt blob ingestován jako jeden dokument.
   
+ **Kontejner úložiště** musí zadat váš účet úložiště a kontejner nebo připojovací řetězec, který se překládá do kontejneru. Připojovací řetězce můžete získat na stránce portálu služby objektů blob.

   ![Definice zdroje dat objektu blob](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - Přeskočení stránky "Obohatit obsah" v průvodci

Přidání kognitivních dovedností (nebo obohacení) není požadavek na import. Pokud nemáte konkrétní potřebu [přidat obohacení AI](cognitive-search-concept-intro.md) do kanálu indexování, měli byste tento krok přeskočit.

Chcete-li krok přeskočit, klikněte na modrá tlačítka v dolní části stránky pro "Další" a "Přeskočit".

### <a name="5---set-index-attributes"></a>5 - Nastavení atributů indexu

Na stránce **Rejstřík** byste měli vidět seznam polí s datovým typem a řadou zaškrtávacích políček pro nastavení atributů indexu. Průvodce může vygenerovat seznam polí na základě metadat a vzorkováním zdrojových dat. 

Atributy můžete hromadně vybrat klepnutím na zaškrtávací políčko v horní části sloupce atributu. Zvolte **Retrievable** a **Searchable** pro každé pole, které má být vráceno do klientské aplikace a podléhá zpracování fulltextového vyhledávání. Všimněte si, že celá čísla nejsou plný text nebo fuzzy prohledávatelné (čísla jsou vyhodnocena doslovně a jsou často užitečné ve filtrech).

Další informace naleznete v popisu [atributů indexu](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) a [analyzátorů jazyků.](https://docs.microsoft.com/rest/api/searchservice/language-support) 

Udělejte si chvilku, abyste si zkontrolovali svůj výběr. Po spuštění průvodce jsou vytvořeny fyzické datové struktury a nebudete moci tato pole upravovat bez přetažení a opětovného vytvoření všech objektů.

   ![Definice indexu objektů blob](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - Vytvořit indexer

Plně zadaný průvodce vytvoří tři odlišné objekty ve vyhledávací službě. Objekt zdroje dat a objekt indexu se uloží jako pojmenované prostředky ve službě Azure Cognitive Search. Poslední krok vytvoří objekt indexeru. Pojmenování indexeru umožňuje, aby existoval jako samostatný prostředek, který můžete plánovat a spravovat nezávisle na indexu a objektu zdroje dat vytvořeném ve stejném sekvenci průvodce.

Pokud nejste obeznámeni s indexery, *indexer* je prostředek v Azure Cognitive Search, který prochází externí zdroj dat pro prohledávatelný obsah. Výstup průvodce **importem dat** je indexer, který prochází zdroj dat JSON, extrahuje prohledávatelný obsah a importuje jej do indexu v Azure Cognitive Search.

   ![Definice indexeru objektů blob](media/search-howto-index-json/import-wizard-json-indexer.png)

Klepnutím na **tlačítko OK** spusťte průvodce a vytvořte všechny objekty. Indexování začíná okamžitě.

Import dat můžete sledovat na stránkách portálu. Oznámení průběhu označují stav indexování a počet nahrávek dokumentů. 

Po dokončení indexování můžete k dotazování indexu použít [Průzkumník ahledatlku.](search-explorer.md)

> [!NOTE]
> Pokud očekávaná data nevidíte, možná budete muset nastavit více atributů ve více polích. Odstraňte index a indexer, které jste právě vytvořili, a znovu projděte průvodce a upravte výběry atributů indexu v kroku 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Použití rozhraní REST API

Rozhraní REST API můžete použít k indexování objektů BLOB JSON podle třídílného pracovního postupu společného všem indexerům v Azure Cognitive Search: vytvořit zdroj dat, vytvořit index, vytvořit indexer. Extrakce dat z úložiště objektů blob dochází při odeslání požadavku vytvořit indexeru. Po dokončení tohoto požadavku budete mít dotazovatelný index. 

Můžete zkontrolovat [rest příklad kódu](#rest-example) na konci této části, která ukazuje, jak vytvořit všechny tři objekty. Tato část také obsahuje podrobnosti o [režimech analýzy JSON](#parsing-modes), [jednotlivých objektech BLOB](#parsing-single-blobs), [polích JSON](#parsing-arrays)a [vnořených polích](#nested-json-arrays).

Pro indexování JSON založené na kódu použijte [Postman](search-get-started-postman.md) a rozhraní REST API k vytvoření těchto objektů:

+ [Index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [zdroj dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Pořadí operací vyžaduje vytvoření a volání objektů v tomto pořadí. Na rozdíl od pracovního postupu portálu vyžaduje přístup kódu dostupný index pro přijetí dokumentů JSON odeslaných prostřednictvím požadavku **Vytvořit indexer.**

Objekty BLOB JSON v úložišti objektů blob Azure jsou obvykle buď jeden dokument JSON nebo JSON "pole". Indexer objektů blob v Azure Cognitive Search můžete analyzovat buď konstrukce, v závislosti na tom, jak nastavit **parametr parsingMode** na požadavek.

| Dokument JSON | režim analýzy | Popis | Dostupnost |
|--------------|-------------|--------------|--------------|
| Jeden na objekt blob | `json` | Analyzuje objekty BLOB JSON jako jeden blok textu. Každý objekt blob JSON se stane jedním dokumentem Azure Cognitive Search. | Obecně dostupné v rozhraní [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API a [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Více násobné na objekt blob | `jsonArray` | Analyzuje pole JSON v objektu blob, kde každý prvek pole se stane samostatný dokument Azure Cognitive Search.  | Obecně dostupné v rozhraní [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API a [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Více násobné na objekt blob | `jsonLines` | Analyzuje objekt blob, který obsahuje více entit JSON ("pole") oddělené novým řádkem, kde se každá entita stane samostatným dokumentem Azure Cognitive Search. | Obecně dostupné v rozhraní [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API a [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 - Montáž vstupů pro požadavek

Pro každý požadavek musíte zadat název služby a klíč správce pro Azure Cognitive Search (v hlavičce POST) a název účtu úložiště a klíč pro úložiště objektů blob. [Postman](search-get-started-postman.md) můžete použít k odesílání požadavků HTTP do Azure Cognitive Search.

Zkopírujte následující čtyři hodnoty do poznámkového bloku, abyste je mohli vložit do požadavku:

+ Název služby Azure Cognitive Search
+ Klíč správce Azure Cognitive Search
+ Azure storage account name
+ Klíč účtu úložiště Azure

Tyto hodnoty najdete na portálu:

1. Na stránkách portálu pro Azure Cognitive Search zkopírujte adresu URL vyhledávací služby ze stránky Přehled.

2. V levém navigačním podokně klikněte na **klávesy** a zkopírujte primární nebo sekundární klíč (jsou ekvivalentní).

3. Přepněte na stránky portálu pro váš účet úložiště. V levém navigačním podokně klikněte v části **Nastavení**na **položku Přístupové klávesy**. Na této stránce je uveden název účtu i klíč. Zkopírujte název účtu úložiště a jeden z klíčů do poznámkového bloku.

### <a name="2---create-a-data-source"></a>2 - Vytvoření zdroje dat

Tento krok poskytuje informace o připojení zdroje dat používané indexerem. Zdroj dat je pojmenovaný objekt v Azure Cognitive Search, který zachová informace o připojení. Typ zdroje dat `azureblob`, určuje, které chování extrakce dat jsou vyvolány indexerem. 

Nahraďte platné hodnoty pro název služby, klíč správce, účet úložiště a zástupné symboly klíče účtu.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 - Vytvoření cílového vyhledávacího indexu 

Indexery jsou spárovány se schématem indexu. Pokud používáte rozhraní API (nikoli portál), připravte index předem, takže jej můžete zadat v operaci indexeru.

Index ukládá prohledávatelný obsah v Azure Cognitive Search. Chcete-li vytvořit index, zadejte schéma, které určuje pole v dokumentu, atributy a další konstrukce, které utvářejí prostředí vyhledávání. Pokud vytvoříte index, který má stejné názvy polí a datové typy jako zdroj, indexer bude odpovídat zdrojová a cílová pole, což vám ušetří práci, která má explicitně mapovat pole.

Následující příklad ukazuje [požadavek vytvořit index.](https://docs.microsoft.com/rest/api/searchservice/create-index) Index bude mít prohledávatelné `content` pole pro uložení textu extrahovaného z objektů BLOB:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 - Konfigurace a spuštění indexeru

Stejně jako u indexu a zdroje dat a indexer je také pojmenovaný objekt, který vytvoříte a znovu použijete ve službě Azure Cognitive Search. Plně zadaný požadavek na vytvoření indexeru může vypadat takto:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Konfigurace indexeru je v těle požadavku. Vyžaduje zdroj dat a prázdný cílový index, který už v Azure Cognitive Search existuje. 

Plán a parametry jsou volitelné. Pokud je vynechete, indexer `json` se spustí okamžitě a použije se jako režim analýzy.

Tento konkrétní indexer nezahrnuje mapování polí. V rámci definice indexeru můžete vynechat **mapování polí,** pokud vlastnosti zdrojového dokumentu JSON odpovídají polím cílového indexu vyhledávání. 


### <a name="rest-example"></a>Příklad REST

Tato část je rekapitulace všech požadavků používaných pro vytváření objektů. Diskuse o součástech naleznete v předchozích částech tohoto článku.

### <a name="data-source-request"></a>Požadavek na zdroj dat

Všechny indexery vyžadují objekt zdroje dat, který poskytuje informace o připojení k existujícím datům. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Požadavek na index

Všechny indexery vyžadují cílový index, který přijímá data. Tělo požadavku definuje schéma indexu, skládající se z polí, které je přiřazeno k podpoře požadovaného chování v indexu s možnostmi vyhledávání. Tento index by měl být prázdný při spuštění indexeru. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Požadavek indexeru

Tento požadavek zobrazuje plně zadaný indexer. Obsahuje mapování polí, která byla v předchozích příkladech vynechána. Připomeňme, že "plán", "parametry" a "fieldMappings" jsou volitelné, pokud je k dispozici výchozí. Vynechání "plán" způsobí, že indexer spustit okamžitě. Vynechání "parsingMode" způsobí, že index použít výchozí "json".

Vytvoření indexeru v Azure Cognitive Search aktivuje import dat. To běží okamžitě, a poté podle plánu, pokud jste za předpokladu, jeden.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

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

Sada .NET SDK má úplnou paritu s rozhraním REST API. Doporučujeme, abyste si přečetli předchozí část rozhraní REST API a zjistili o konceptech, pracovních postupech a požadavcích. Potom můžete odkazovat na následující dokumentaci reference rozhraní .NET API k implementaci indexeru JSON ve spravovaném kódu.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Režimy analýzy

Objekty BLOB JSON mohou převzít více formulářů. Parametr **parsingMode** v indexeru JSON určuje, jak je obsah objektu blob JSON analyzován a strukturován v indexu Azure Cognitive Search:

| režim analýzy | Popis |
|-------------|-------------|
| `json`  | Indexujte každý objekt blob jako jeden dokument. Toto nastavení je výchozí. |
| `jsonArray` | Tento režim zvolte, pokud se objekty BLOB skládají z polí JSON a potřebujete, aby se každý prvek pole stal samostatným dokumentem v Azure Cognitive Search. |
|`jsonLines` | Tento režim zvolte, pokud se objekty BLOB skládají z více entit JSON, které jsou oddělené novým řádkem, a potřebujete, aby se každá entita stala samostatným dokumentem v Azure Cognitive Search. |

Ve výsledcích hledání si můžete dokument usuzovat jako jednu položku. Pokud chcete, aby se každý prvek v poli zobrazoval ve `jsonArray` výsledcích hledání jako nezávislá položka, použijte podle potřeby možnost nebo. `jsonLines`

V rámci definice indexeru můžete volitelně použít [mapování polí](search-indexer-field-mappings.md) k výběru vlastností zdrojového dokumentu JSON, které se použijí k naplnění cílového indexu vyhledávání. V `jsonArray` režimu analýzy, pokud pole existuje jako vlastnost nižší úrovně, můžete nastavit kořen dokumentu označující, kde je pole umístěno v objektu blob.

> [!IMPORTANT]
> Při použití `json` `jsonArray` , `jsonLines` nebo režim analýzy Azure Cognitive Search předpokládá, že všechny objekty BLOB ve zdroji dat obsahují JSON. Pokud potřebujete podporovat kombinaci objektů BLOB JSON a non-JSON ve stejném zdroji dat, dejte nám vědět na [našich stránkách UserVoice](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Analyzovat jednotlivé objekty BLOB JSON

Ve výchozím nastavení [indexer objektů blob Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md) analyzuje objekty BLOB JSON jako jeden blok textu. Často chcete zachovat strukturu dokumentů JSON. Předpokládejme například, že máte následující dokument JSON v úložišti objektů Blob Azure:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Indexer objektů blob analyzuje dokument JSON do jednoho dokumentu Azure Cognitive Search. Indexer načte index porovnáním "text", "datePublished" a "tagy" ze zdroje proti identicky pojmenovaný a zadaný cílový index pole.

Jak již bylo uvedeno, mapování polí není vyžadováno. Vzhledem k tomu, index s "text", "datePublished a "značky" pole, indexer objektů blob můžete odvodit správné mapování bez mapování polí v požadavku.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analyzovat pole JSON

Alternativně můžete použít možnost pole JSON. Tato možnost je užitečná, když objekty BLOB obsahují *pole dobře formátovaných objektů JSON*a chcete, aby se každý prvek stal samostatným dokumentem Azure Cognitive Search. Například vzhledem k následujícímu objektu blob JSON můžete naplnit index Azure Cognitive Search třemi samostatnými dokumenty, z nichž každý má pole "id" a "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Pro pole JSON definice indexeru by měl vypadat podobně jako v následujícím příkladu. Všimněte si, že parametr parsingMode určuje `jsonArray` analyzátor. Určení pravého analyzátoru a správného zadávání dat jsou pouze dva požadavky specifické pro pole pro indexování objektů BLOB JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Opět všimněte si, že mapování polí lze vynechat. Za předpokladu, že index s identicky s názvem "id" a "text" pole, indexer objektů blob můžete odvodit správné mapování bez seznamu explicitní mapování polí.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Analyzovat vnořená pole
Pro pole JSON s vnořené `documentRoot` prvky, můžete zadat k označení víceúrovňové struktury. Pokud například objekty BLOB vypadají takto:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Tato konfigurace slouží k indexování `level2` pole obsaženého ve vlastnosti:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Objekty BLOB analýzy oddělené novými řádky

Pokud váš objekt blob obsahuje více entit JSON oddělených novým řádkem a chcete, aby se každý prvek stal samostatným dokumentem Azure Cognitive Search, můžete se rozhodnout pro možnost řádků JSON. Například vzhledem k následujícímu objektu blob (kde existují tři různé entity JSON), můžete naplnit index Azure Cognitive Search třemi samostatnými dokumenty, z nichž každý má pole "id" a "text".

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

Pro řádky JSON definice indexeru by měl vypadat podobně jako v následujícím příkladu. Všimněte si, že parametr parsingMode určuje `jsonLines` analyzátor. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Opět všimněte si, že mapování polí může `jsonArray` být vynecháno, podobně jako režim analýzy.

## <a name="add-field-mappings"></a>Přidání mapování polí

Pokud zdrojová a cílová pole nejsou dokonale zarovnána, můžete definovat oddíl mapování polí v těle požadavku pro explicitní přidružení pole k poli.

V současné době Azure Cognitive Search nelze indexovat libovolné dokumenty JSON přímo, protože podporuje pouze primitivní datové typy, pole řetězců a geojson body. Mapování polí však můžete použít k **vyskladnění** částí dokumentu JSON a jejich "zvednutí" do polí nejvyšší úrovně vyhledávacího dokumentu. Informace o základech mapování polí najdete [v tématu Mapování polí v indexerech Azure Cognitive Search](search-indexer-field-mappings.md).

Přehodnocení našeho příkladu dokumentu JSON:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Předpokládejme index vyhledávání s `text` následujícími `Edm.String` `date` poli: `Edm.DateTimeOffset`typu `tags` , `Collection(Edm.String)`typu a typu . Všimněte si rozdílu mezi "datePublished" ve zdroji a `date` pole v indexu. Chcete-li namapovat zařízení JSON na požadovaný tvar, použijte následující mapování polí:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Názvy zdrojových polí v mapování chytají pomocí zápisu [ukazatele JSON.](https://tools.ietf.org/html/rfc6901) Začnete lomítkem, které odkazuje na kořen dokumentu JSON, a pak vyberete požadovanou vlastnost (na libovolné úrovni vnoření) pomocí cesty oddělené lomítkem.

Můžete také odkazovat na jednotlivé prvky pole pomocí indexu založeného na nule. Chcete-li například vybrat první prvek pole "značky" z výše uvedeného příkladu, použijte mapování pole, jako je toto:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Pokud název zdrojového pole v cestě mapování pole odkazuje na vlastnost, která v JSON neexistuje, je toto mapování přeskočeno bez chyby. To se provádí tak, abychom mohli podporovat dokumenty s jiným schématem (což je běžný případ použití). Vzhledem k tomu, že neexistuje žádné ověření, je třeba dbát, aby se zabránilo překlepy ve specifikaci mapování pole.
>
>

## <a name="see-also"></a>Viz také

+ [Indexery ve službě Azure Cognitive Search](search-indexer-overview.md)
+ [Indexování úložiště objektů blob Azure pomocí Azure Cognitive Search](search-howto-index-json-blobs.md)
+ [Indexování objektů BLOB CSV pomocí indexátoru objektů blob Azure Cognitive Search](search-howto-index-csv-blobs.md)
+ [Kurz: Hledání částečně strukturovaných dat z úložiště objektů blob Azure](search-semi-structured-data.md)
