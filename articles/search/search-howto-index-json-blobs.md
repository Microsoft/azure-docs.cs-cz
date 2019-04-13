---
title: Indexování objektů BLOB JSON z objektů Blob v Azure indexeru pro fulltextové vyhledávání – Azure Search
description: Procházení objektů BLOB Azure JSON pro textový obsah pomocí indexeru Azure Search Blob. Indexery můžete automatizovat příjem dat pro vybrané zdroje dat jako úložiště objektů Blob v Azure.
ms.date: 04/11/2019
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 6db86d3e5aba1a2e43e69e71df8cc516fb14581f
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527349"
---
# <a name="how-to-index-json-blobs-using-azure-search-blob-indexer"></a>Jak indexovat objektů BLOB JSON pomocí indexeru Azure Search Blob
V tomto článku se dozvíte, jak nakonfigurovat Azure Search blob [indexer](search-indexer-overview.md) extrahujte strukturované obsah z dokumentů JSON ve službě Azure Blob storage a usnadnit prohledávatelná ve službě Azure Search. Tento pracovní postup vytvoří index Azure Search a načte se existující text extrahovaný z objektů BLOB JSON. 

Můžete použít [portál](#json-indexer-portal), [rozhraní REST API](#json-indexer-rest), nebo [sady .NET SDK](#json-indexer-dotnet) do indexu obsah JSON. Společná pro všechny přístupy je, že dokumenty JSON jsou umístěny v kontejneru objektů blob v účtu služby Azure Storage. Pokyny k odesílání dokumentů JSON z jiných platforem než Azure najdete v tématu [import dat ve službě Azure Search](search-what-is-data-import.md).

Objekty BLOB JSON ve službě Azure Blob storage jsou obvykle jednotlivý dokument JSON nebo kolekci entit JSON. Pro kolekce JSON, objekt blob může mít **pole** elementů JSON ve správném formátu. Objekty BLOB může také obsahovat více jednotlivých entit JSON oddělené nový řádek. Indexování objektů blob ve službě Azure Search může analyzovat tyto konstrukce, v závislosti na tom, jak nastavit **parsingMode** parametru na požadavek.

> [!IMPORTANT]
> `json` a `jsonArray` režimy parsování jsou obecně dostupné, ale `jsonLines` režim parsování je ve verzi public preview a neměli byste používat v produkčním prostředí. Další informace najdete v tématu [REST api-version = 2017-11-11-Preview](search-api-2017-11-11-preview.md). 

> [!NOTE]
> Postupujte podle doporučení konfigurace indexeru v [jeden mnoho indexování](search-howto-index-one-to-many-blobs.md) výstup více dokumentů vyhledávání z jednoho objektu blob Azure.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Použití portálu

Nejjednodušším způsobem pro indexování dokumentů JSON je použití Průvodce v [webu Azure portal](https://portal.azure.com/). Pomocí analýzy metadat v kontejneru objektů blob v Azure [ **importovat data** ](search-import-data-portal.md) průvodce můžete vytvořit výchozí index, mapují pole zdroje na cíl pole indexu a načtení indexu v rámci jedné operace. V závislosti na velikosti a složitosti zdroje dat může mít indexu provozní fulltextové vyhledávání v minutách.

Doporučujeme používat stejné předplatné Azure pro Azure Search a Azure storage, pokud možno ve stejné oblasti.

### <a name="1---prepare-source-data"></a>1 – Příprava zdrojových dat

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/).

1. [Vytvořte kontejner objektů Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) obsahující vaše data. Úroveň veřejného přístupu můžete nastavit na některou z jeho platných hodnot.

Budete potřebovat název účtu úložiště, název kontejneru a přístupový klíč pro načítání dat **importovat data** průvodce.

### <a name="2---start-import-data-wizard"></a>2 – Spusťte Průvodce importem dat

Na stránce Přehled služby Azure Search můžete [spusťte průvodce](search-import-data-portal.md) z příkazového řádku, nebo kliknutím **přidat Azure Search** v **službu Blob service** část vaší levé navigační podokno prvku účtu úložiště.

   ![Příkaz pro import dat na portálu](./media/search-import-data-portal/import-data-cmd2.png "spusťte Průvodce importem dat")

### <a name="3---set-the-data-source"></a>3 – nastavení zdroje dat

V **zdroj dat** stránku, musí být zdroj **Azure Blob Storage**, s následujícími specifikacemi:

+ **Data k extrakci** by měl být *obsah a metadata*. Pokud vyberete tuto možnost umožňuje Průvodce odvodit schéma indexu a mapování polí pro import.
   
+ **Režim parsování** by mělo být nastavené *JSON*, *pole JSON* nebo *řádků JSON*. 

  *JSON* zabývá se výhodami jednotlivých objektů blob jako dokument hledání jednoduchého, objeví jako nezávislou položku ve výsledcích hledání. 

  *Pole JSON* je pro objekty BLOB, které obsahují ve správném formátu dat JSON – ve správném formátu JSON odpovídá pole objektů, nebo má vlastnost, která je pole objektů, a chcete, aby každý prvek na kloubové jako samostatná, nezávislé hledání v dokumentech. Pokud objekty BLOB jsou komplexní a nevyberete *pole JSON* celý objekt blob se ingestuje jako jeden dokument.

  *Řádky JSON* je pro objekty BLOB skládá z několika entit JSON oddělené nový řádek, kde má každá entita na kloubové jako samostatné nezávislé Prohledat dokument. Pokud objekty BLOB jsou komplexní a nevyberete *řádků JSON* analýze režimu a potom celý objekt blob se ingestuje jako jeden dokument.
   
+ **Kontejner úložiště** musíte zadat svůj účet úložiště a kontejner nebo připojovací řetězec, který se přeloží do kontejneru. Na stránce portálu služby objektů Blob můžete získat připojovací řetězce.

   ![Definice zdroje dat objektu BLOB](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 – přeskočte stránku "Přidat kognitivní vyhledávání" v Průvodci

Přidat kognitivní dovednosti není nutné pro import dokumentu JSON. Pokud nemáte specifickou potřebu [patří rozhraní API služeb Cognitive Services a transformace](cognitive-search-concept-intro.md) na váš kanál indexování by měl tento krok přeskočit.

Chcete-li přeskočit krok, nejdřív přejdete na další stránku.

   ![Tlačítko Další stránky pro kognitivního vyhledávání](media/search-get-started-portal/next-button-add-cog-search.png)

Z této stránky můžete přeskočit přímo k přizpůsobení indexu.

   ![Vynechání kroku kognitivních dovedností](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 - atributy indexu set

V **Index** stránky, zobrazí se seznam polí s typem dat a řadu zaškrtávací políčka pro nastavení atributy indexu. Průvodce můžete vytvořit seznam polí na základě metadat a vzorkováním zdrojová data. 

Vám může hromadně výběru atributy kliknutím na zaškrtávací políčko v horní části sloupce atributu. Zvolte **Retrievable** a **Searchable** pro každé pole, která má být vrácen pro klientskou aplikaci a v souladu s zpracování hledání textu v plném znění. Všimněte si, že celá čísla nejsou textu v plném znění nebo přibližné prohledávatelná (čísla jsou vyhodnocovány znění a jsou často užitečné při filtry).

Zkontrolujte popis [atributy indexu](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) a [jazykové analyzátory](https://docs.microsoft.com/rest/api/searchservice/language-support) Další informace. 

Za chvíli zkontrolujte zvolené položky. Po spuštění Průvodce fyzické datové struktury jsou vytvořeny a nebudou moct tato pole upravovat bez vyřadit a znovu vytvořit všechny objekty.

   ![Definice indexu objektů BLOB](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 – Vytvoření indexeru

Plně zadaný, Průvodce vytvoří tři různé objekty ve vyhledávací službě. Objekt zdroje dat a indexu objektu se ukládají jako pojmenovaným prostředkům ve službě Azure Search. Poslední krok vytvoří objekt indexeru. Pojmenování indexer umožňuje existuje jako samostatný prostředek, který můžete naplánovat a spravovat bez ohledu na jejich rejstřík a data zdrojový objekt, vytvoří ve stejném pořadí průvodce.

Pokud nejste obeznámeni s indexery, *indexer* je prostředek ve službě Azure Search, která prochází externího zdroje dat pro prohledávatelný obsah. Výstup **importovat data** Průvodce indexer je výsledkem, který prochází zdroje dat JSON, extrahuje prohledávatelný obsah a naimportuje do indexu Azure Search.

   ![Definice indexeru BLOB](media/search-howto-index-json/import-wizard-json-indexer.png)

Klikněte na tlačítko **OK** ke spuštění průvodce a vytvoření všech objektů. Indexování začíná okamžitě.

Můžete monitorovat import dat do stránky portálu. Oznámení o průběhu označuje stav indexování a kolik dokumenty jsou odeslány. 

Při indexování hotový, můžete použít [Průzkumníka služby Search](search-explorer.md) k dotazování indexu.

> [!NOTE]
> Pokud nevidíte data, která jste očekávali, můžete potřebovat nastavit další atributy na více polí. Odstranění indexu a indexeru, kterou jste právě vytvořili a postupujte podle pokynů průvodce znovu, vyberte požadované možnosti pro atributy indexu v kroku 5 úpravy. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Použití rozhraní REST API

Rozhraní REST API můžete použít k indexování objektů BLOB JSON následujícího pracovního postupu třemi částmi společné pro všechny indexery ve službě Azure Search: vytvoření zdroje dat, vytvoření indexu, vytvořením indexeru. Extrakce dat z úložiště objektů blob nastane, když odešlete žádost o vytvoření indexeru. Po dokončení této žádosti budete mít dotazovatelné indexu. 

Můžete zkontrolovat [REST ukázkový kód](#rest-example) na konci této části, která ukazuje, jak vytvářet všechny tři objekty. Tato část také obsahuje podrobnosti o [režimy parsování JSON](#parsing-modes), [jednoduché objekty BLOB](#parsing-single-blobs), [pole JSON](#parsing-arrays), a [vnořená pole](#nested-json-arrays).

Založený na kódu JSON indexování, použijte [Postman](search-fiddler.md) a rozhraní REST API k vytvoření těchto objektů:

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Zdroj dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Pořadí operací vyžaduje vytvoření a volání objekty v tomto pořadí. Rozdíl od portálu pracovního postupu, vyžaduje přístup založený na kódu k dispozici indexu tak, aby přijímal odeslaných dokumentů JSON **vytvoření indexeru** požadavku.

Objekty BLOB JSON ve službě Azure Blob storage jsou obvykle jednotlivý dokument JSON nebo JSON "pole". Indexování objektů blob ve službě Azure Search můžete analyzovat buď konstrukce, v závislosti na tom, jak nastavit **parsingMode** parametru na požadavek.

| Dokument JSON | parsingMode | Popis | Dostupnost |
|--------------|-------------|--------------|--------------|
| Jeden objekt blob | `json` | Analyzuje objektů BLOB JSON jako jediný neodkazovaný blok textu. Každý objekt blob JSON se změní na jeden dokument Azure Search. | Obecně dostupná v obou [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) rozhraní API a [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Více na jeden objekt blob | `jsonArray` | Analyzuje pole JSON v objektu blob, kde každý prvek pole se změní na samostatné dokument Azure Search.  | K dispozici ve verzi preview v obou [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) rozhraní API a [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Více na jeden objekt blob | `jsonLines` | Analyzuje objektu blob, který obsahuje více entit JSON ("pole") oddělené nového řádku, kde každá entita se změní na samostatné dokument Azure Search. | K dispozici ve verzi preview v obou [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) rozhraní API a [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 - vstupy pro žádost o sestavení

Pro každý požadavek musíte zadat název služby a klíč správce pro Azure Search (v hlavičce POST) a název účtu úložiště a klíč pro úložiště objektů blob. Můžete použít [Postman](search-fiddler.md) k odesílání požadavků HTTP do služby Azure Search.

Zkopírujte následující čtyři hodnoty do poznámkového bloku tak, aby vložte je do požadavku:

+ Název služby Azure Search
+ Klíč správce služby Azure Search
+ Název účtu úložiště Azure
+ Klíč účtu úložiště Azure

Tyto hodnoty můžete najít na portálu:

1. Na stránkách portálu pro Azure Search zkopírujte adresu URL služby search na stránce Přehled.

2. V levém navigačním podokně klikněte na tlačítko **klíče** a poté zkopírujte primární nebo sekundární klíč (jsou ekvivalentní).

3. Přepnout na stránkách portálu pro váš účet úložiště. V levém navigačním podokně v části **nastavení**, klikněte na tlačítko **přístupové klíče**. Tato stránka obsahuje název účtu a klíč. Název účtu úložiště a jeden z klíčů zkopírujte do poznámkového bloku.

### <a name="2---create-a-data-source"></a>2 – Vytvoření zdroje dat

Tento krok obsahuje zdroje informací o připojení dat používaný indexerem. Zdroj dat je pojmenovaný objekt ve službě Azure Search, která udržuje informace o připojení. Typ, zdroje dat `azureblob`, určuje, jaké chování extrakce dat jsou vyvolány pomocí indexeru. 

Nahraďte platné hodnoty pro název služby, klíč správce, účet úložiště a účet klíče zástupné symboly.

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3. vytvoření cílovým indexem vyhledávání 

Indexery jsou párovány s schématu indexu. Pokud používáte rozhraní API (a ne na portálu), připravte indexu předem tak, aby jej zadat v operaci indexeru.

Index ukládá prohledávatelný obsah ve službě Azure Search. K vytvoření indexu, zadejte schéma, které určuje pole v dokumentu, atributy a jiných objektů, které obrazce vyhledávání. Pokud vytvoříte index, který má stejné názvy polí a datové typy jako zdroj, indexer bude odpovídat pole zdrojové a cílové uložení práce by bylo nutné explicitně mapování polí.

Následující příklad ukazuje [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) požadavku. Index bude mít možností prohledávání `content` pole, které chcete uložit text extrahovaný z objektů blob:   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 – konfigurace a spuštění indexeru

Stejně jako u indexu a datové zdroje a indexeru je také na pojmenovaný objekt, který vytvoříte a opakovaně použít na služby Azure Search. Plně zadaný požadavek na vytvoření indexeru může vypadat takto:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Konfigurace indexeru je v textu požadavku. Vyžaduje zdroje dat a index prázdný cíl, který již existuje ve službě Azure Search. 

Plán a parametry jsou volitelné. Vynecháte-li je, spuštění indexeru okamžitě, pomocí `json` analýzy režim.

Tento konkrétní indexer neobsahuje mapování polí. V rámci definice indexeru můžete nechat **mapování polí** Pokud pole cílovým indexem vyhledávání neodpovídají vlastnosti ve zdrojovém dokumentu JSON. 


### <a name="rest-example"></a>Příklad REST

Tato část je rekapitulace toho, všechny požadavky pro vytváření objektů. Diskuzi o součásti najdete v předchozí části tohoto článku.

### <a name="data-source-request"></a>Požadavky na zdroje dat

Všechny indexery vyžadují objekt zdroje dat, který poskytuje informace o připojení k existující data. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Požadavek na index

Všechny indexery vyžadují cílový index, který přijímá data. Text žádosti definuje schéma indexu, který se skládá z pole s přidělenými atributy pro podporu požadovaného chování v prohledávatelný index. Při spuštění indexeru, by měl být tento index prázdný. 

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Žádost o indexeru

Tento požadavek zobrazuje indexeru se plně zadaný. Obsahuje mapování polí, které byly vynechány v předchozích příkladech. Odvolat tento "plán", "parametrů" a "fieldMappings" jsou volitelné, dokud není k dispozici výchozí. Vynechání "plán" způsobí, že indexer spustit okamžitě. Vynechání "parsingMode" způsobí, že index, který chcete použít výchozí nastavení "json".

Vytvoření indexeru Azure Search aktivuje data importovat. Pokud jste zadali jednu poběží podle plánu okamžitě a po tomto datu.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

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

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Režimy parsování

Objekty BLOB JSON, můžete předpokládat více formulářů. **ParsingMode** parametr pro JSON indexer Určuje, jak obsah objektu blob JSON je analyzovat a strukturované v indexu Azure Search:

| parsingMode | Popis |
|-------------|-------------|
| `json`  | Každý objekt blob indexu jako jeden dokument. Toto je výchozí. |
| `jsonArray` | Zvolte tento režim, pokud objektů BLOB obsahovat pole JSON, a je třeba každý prvek pole se samostatnou dokumentu ve službě Azure Search. |
|`jsonLines` | V tomto režimu vyberte, pokud objektů blob se skládají z více entit, JSON, které jsou oddělené znakem nového řádku, a je nutné, aby každá entita se samostatnou dokumentu ve službě Azure Search. |

Dokument si lze představit jako jedinou položku ve výsledcích hledání. Pokud chcete, aby každý prvek v poli zobrazení ve výsledcích hledání jako nezávislou položku, použijte `jsonArray` nebo `jsonLines` možnost podle potřeby.

V rámci definice indexeru můžete volitelně použít [mapování polí](search-indexer-field-mappings.md) zvolte Vlastnosti ve zdrojovém dokumentu JSON, které se používají k naplnění cílovým indexem vyhledávání. Pro `jsonArray` analýze režimu, pokud existuje pole jako vlastnost nižší úrovně, můžete nastavit kořen dokumentu určující umístění pole v rámci objektu blob.

> [!IMPORTANT]
> Při použití `json`, `jsonArray` nebo `jsonLines` režim parsování Azure Search se předpokládá, že všechny objekty BLOB ve zdroji dat obsahovat JSON. Pokud potřebujete podporovat kombinaci objekty BLOB JSON a jiné JSON do stejného zdroje dat, dejte nám vědět o [náš web UserVoice](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Analyzovat jeden objektů BLOB JSON

Ve výchozím nastavení [indexeru Azure Search blob](search-howto-indexing-azure-blob-storage.md) analyzuje objektů BLOB JSON jako jediný neodkazovaný blok textu. Často chcete zachovat strukturu dokumentů JSON. Předpokládejme například, že máte následující dokument JSON ve službě Azure Blob storage:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Indexování objektů blob analyzuje dokument JSON do jednoho dokumentu Azure Search. Indexer načte index to provede spárováním odpovídajících "text", "datePublished" a "značek" ze zdroje proti cílové identicky názvem a typem indexu pole.

Jak je uvedeno, nejsou nutné mapování polí. Zadaný index s "text", "datePublished a"značek"polí, objekt blob indexer lze odvodit správné mapování bez pole mapování v požadavku.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analyzovat pole JSON

Alternativně můžete použít možnost pole JSON. Tato možnost je užitečná, pokud objekty BLOB obsahují *pole objektů JSON ve správném formátu*, a chcete, aby každý prvek stane samostatnou dokumentů Azure Search. Například s ohledem následujících objektů blob JSON, která můžete naplnit index Azure Search pomocí tří samostatných dokumentů, každý s pole "id" a "text".  

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

## <a name="parse-nested-arrays"></a>Analyzovat vnořená pole
Pro JSON pole s vnořené elementy, můžete zadat `documentRoot` k označení víceúrovňových struktury. Pokud například objektů BLOB vypadat nějak takto:

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

## <a name="parse-blobs-separated-by-newlines"></a>Analyzovat oddělené vložení znaků newline objekty BLOB

Pokud objekt blob obsahuje více entit JSON oddělené nový řádek, a chcete, aby každý prvek stane samostatnou dokumentů Azure Search, můžete se rozhodnout pro možnost řádků JSON. Mějme například následující objekt blob (pokud existují tři různé entity JSON), která můžete naplnit index Azure Search pomocí tří samostatných dokumentů, každý s pole "id" a "text".

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

Pro řádky JSON by měl vypadat podobně jako v následujícím příkladu definice indexeru. Všimněte si, že parsingMode parametr určuje, `jsonLines` analyzátor. 

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Opět si všimněte, že mapování polí můžou být vynechán, podobně jako `jsonArray` režim parsování.

## <a name="add-field-mappings"></a>Přidat mapování polí

Při zdrojové a cílové pole nejsou zarovnány dokonale, můžete definovat části mapování pole v textu požadavku pro explicitní přiřazení jednoho pole.

V současné době Azure Search nemůže indexovat libovolné dokumenty JSON přímo podporuje pouze primitivní datové typy, pole řetězce a GeoJSON body. Můžete však použít **mapování polí** "přenést" je do nejvyšší úrovně pole hledání v dokumentech a výběr součástí dokumentu JSON. Další informace o základní informace o mapování polí najdete v tématu [mapování polí v indexerech Azure Search](search-indexer-field-mappings.md).

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

## <a name="see-also"></a>Další informace najdete v tématech

+ [Indexery ve službě Azure Search](search-indexer-overview.md)
+ [Indexování služby Azure Blob Storage pomocí služby Azure Search](search-howto-index-json-blobs.md)
+ [Indexování objektů BLOB CSV pomocí indexeru Azure Search blob](search-howto-index-csv-blobs.md)
+ [Kurz: Prohledávání částečně strukturovaných dat z úložiště objektů Blob v Azure](search-semi-structured-data.md)
