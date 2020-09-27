---
title: Hledání v blobech JSON
titleSuffix: Azure Cognitive Search
description: Procházení objektů BLOB služby Azure JSON pro obsah textu pomocí indexeru objektů BLOB v Azure Kognitivní hledání. Indexery automatizují přijímání dat pro vybrané zdroje dat, jako je Azure Blob Storage.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: c3aa813c671e9755b6ab65ba1e2967876bd170ca
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398336"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Indexování objektů BLOB JSON pomocí indexeru objektů BLOB v Azure Kognitivní hledání

V tomto článku se dozvíte, jak nakonfigurovat [indexer](search-indexer-overview.md) objektů BLOB v Azure kognitivní hledání pro extrakci strukturovaného obsahu z dokumentů JSON ve službě Azure Blob Storage a zpřístupnění v Azure kognitivní hledání. Tento pracovní postup vytvoří index služby Azure Kognitivní hledání a načte ho s existujícím textem extrahovaným z objektů BLOB JSON. 

K indexování obsahu JSON můžete použít [portál](#json-indexer-portal), [rozhraní REST API](#json-indexer-rest)nebo [sadu .NET SDK](#json-indexer-dotnet) . Společné pro všechny přístupy je, že dokumenty JSON se nacházejí v kontejneru objektů BLOB v Azure Storagem účtu. Pokyny k doručování dokumentů JSON z jiných platforem mimo Azure najdete v tématu [Import dat do azure kognitivní hledání](search-what-is-data-import.md).

Objekty blob JSON ve službě Azure Blob Storage jsou obvykle buď jedním dokumentem JSON (režim analýzy je `json` ), nebo kolekcí entit JSON. V případě kolekcí může objekt BLOB obsahovat **pole** prvků JSON ve správném formátu (režim analýzy je `jsonArray` ). Objekty blob mohou být také tvořeny více jednotlivými entitami JSON oddělenými znakem nového řádku (režim analýzy je `jsonLines` ). Parametr **parsingMode** na žádosti určuje výstupní struktury.

> [!NOTE]
> Další informace o indexování více dokumentů hledání z jednoho objektu BLOB naleznete v tématu [indexování 1: n](search-howto-index-one-to-many-blobs.md).

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Použití portálu

Nejjednodušší způsob indexování dokumentů JSON je použití Průvodce v [Azure Portal](https://portal.azure.com/). Díky analýze metadat v kontejneru objektů BLOB v Azure může průvodce [**importem dat**](search-import-data-portal.md) vytvořit výchozí index, mapovat zdrojová pole na pole cílového indexu a načíst index v rámci jedné operace. V závislosti na velikosti a složitosti zdrojových dat můžete mít během několika minut provozní fulltextový index vyhledávání.

Pro Azure Kognitivní hledání i Azure Storage doporučujeme použít pro nižší latenci stejnou oblast nebo umístění a vyhnout se tak poplatkům za šířku pásma.

### <a name="1---prepare-source-data"></a>1. Příprava zdrojových dat

[Přihlaste se k Azure Portal](https://portal.azure.com/) a [vytvořte kontejner objektů BLOB](../storage/blobs/storage-quickstart-blobs-portal.md) , který bude obsahovat vaše data. Úroveň veřejného přístupu může být nastavena na libovolnou z jeho platných hodnot.

V průvodci **importem dat** budete potřebovat název účtu úložiště, název kontejneru a přístupový klíč k načtení dat.

### <a name="2---start-import-data-wizard"></a>2. spuštění Průvodce importem dat

Na stránce Přehled služby Search můžete [Spustit Průvodce](search-import-data-portal.md) z panelu příkazů.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd2.png" alt-text="Příkaz Importovat data na portálu" border="false":::

### <a name="3---set-the-data-source"></a>3 – nastavení zdroje dat

Na stránce **zdroj dat** musí být ve zdroji **BLOB Storage Azure**, a to s následujícími specifikacemi:

+ **Data, která se mají extrahovat** , by měla být *obsah a metadata*. Výběrem této možnosti umožníte průvodci odvodit schéma indexu a namapovat pole pro import.
   
+ **Režim analýzy** by měl být nastaven na *JSON*, *pole JSON* nebo *řádky JSON*. 

  *JSON* kloubuje každý objekt BLOB jako jeden vyhledávací dokument, který ve výsledcích vyhledávání zobrazuje jako nezávislou položku. 

  *Pole JSON* je pro objekty blob, které obsahují dobře vytvořená data JSON – ve správném formátu JSON odpovídá poli objektů, nebo má vlastnost, která je pole objektů a chcete, aby byl každý prvek kloubově sestaven jako samostatný nezávislý vyhledávací dokument. Pokud jsou objekty blob složité a nevolíte *pole JSON* , bude se celý objekt BLOB ingestovat jako jeden dokument.

  *Řádky JSON* jsou pro objekty blob složené z více entit JSON oddělených novou čárou, kde chcete, aby se jednotlivé entity nakloubly jako samostatné nezávislé hledané dokumenty. Pokud jsou objekty blob složité a nevolíte režim analýzy *řádků JSON* , celý objekt BLOB se ingestuje jako jeden dokument.
   
+ **Kontejner úložiště** musí určovat váš účet úložiště a kontejner nebo připojovací řetězec, který se překládá na kontejner. Připojovací řetězce můžete získat na stránce Blob serviceového portálu.

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-data-source.png" alt-text="Definice zdroje dat objektu BLOB" border="false":::

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 – přeskočí stránku "obohacení obsahu" v Průvodci

Přidání dovedností rozpoznávání (nebo obohacení) není požadavkem na import. Pokud nemáte konkrétní nutnost [Přidat rozšíření AI](cognitive-search-concept-intro.md) do kanálu indexování, měli byste tento krok přeskočit.

Chcete-li tento krok přeskočit, klikněte na modré tlačítka v dolní části stránky pro možnost "Další" a "Přeskočit".

### <a name="5---set-index-attributes"></a>5. nastavení atributů indexu

Na stránce **index** byste měli vidět seznam polí s datovým typem a řadu zaškrtávacích políček pro nastavení atributů indexu. Průvodce může vygenerovat seznam polí založený na metadatech a vzorkováním zdrojových dat. 

Atributy můžete hromadně vybírat kliknutím na zaškrtávací políčko v horní části sloupce atributu. Vyberte možnost získatelné a **prohledávatelné** pro každé pole, které by se mělo vrátit do klientské **aplikace a podléhá** fulltextovým zpracování fulltextového vyhledávání. Všimnete si, že celá čísla nejsou fulltextová nebo přibližná prohledávání (čísla jsou vyhodnocována v doslovném znění a jsou často užitečná ve filtrech).

Další informace najdete v popisu [atributů indexu](/rest/api/searchservice/create-index#bkmk_indexAttrib) a [analyzátorů jazyka](/rest/api/searchservice/language-support) . 

Věnujte prosím chvíli kontrole vašich výběrů. Po spuštění Průvodce se vytvoří fyzické datové struktury a nebudete moct tato pole upravovat, aniž byste museli odstraňovat a znovu vytvářet všechny objekty.

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-index.png" alt-text="Definice indexu objektu BLOB" border="false":::

### <a name="6---create-indexer"></a>6. vytvoření indexeru

V rámci vaší vyhledávací služby vytvoří průvodce tři odlišné objekty. Objekt zdroje dat a objekt indexu se ukládají jako pojmenované prostředky ve službě Azure Kognitivní hledání. Poslední krok vytvoří objekt indexeru. Pojmenování indexeru umožňuje, aby existoval jako samostatný prostředek, který můžete naplánovat a spravovat nezávisle na objektu index a zdroj dat, který jste vytvořili ve stejné sekvenci průvodce.

Pokud nejste obeznámeni s indexery, *indexer* je prostředkem v Azure kognitivní hledání, který prochází externím zdrojem dat pro prohledávatelný obsah. Výstupem průvodce **importem dat** je indexer, který prochází váš zdroj dat JSON, extrahuje prohledávatelný obsah a importuje ho do indexu v Azure kognitivní hledání.

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-indexer.png" alt-text="Definice indexeru objektů BLOB" border="false":::

Kliknutím na tlačítko **OK** spusťte průvodce a vytvořte všechny objekty. Indexování se okamžitě zahájí.

Data importování můžete monitorovat na stránkách portálu. Oznámení o průběhu označují stav indexování a počet odeslaných dokumentů. 

Po dokončení indexování můžete pomocí [Průzkumníka služby Search Vyhledat](search-explorer.md) dotaz na svůj index.

> [!NOTE]
> Pokud nevidíte očekávaná data, možná budete muset nastavit další atributy pro více polí. Odstraňte index a indexer, který jste právě vytvořili, a projděte průvodce znovu a změňte si výběr pro atributy indexu v kroku 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Použití rozhraní REST API

Můžete použít REST API k indexování objektů BLOB JSON, a to za pracovní postup tří částí, který je společný pro všechny Indexery v Azure Kognitivní hledání: vytvořte zdroj dat, vytvořte index a vytvořte indexer. Při odeslání žádosti o vytvoření indexeru dojde k extrakci dat z úložiště objektů BLOB. Po dokončení této žádosti budete mít Queryable index. 

Na konci této části si můžete prohlédnout [ukázkový kód REST](#rest-example) , který ukazuje, jak vytvořit všechny tři objekty. Tato část obsahuje také podrobnosti o [režimech analýzy JSON](#parsing-modes), samostatných objektech [BLOB](#parsing-single-blobs), [polích JSON](#parsing-arrays)a [vnořených polích](#nested-json-arrays).

Pro indexování JSON založené na kódu použijte příkaz [post](search-get-started-postman.md) a REST API k vytvoření těchto objektů:

+ [indexovacím](/rest/api/searchservice/create-index)
+ [zdroj dat](/rest/api/searchservice/create-data-source)
+ [indexer](/rest/api/searchservice/create-indexer)

Pořadí operací vyžaduje, abyste v tomto pořadí vytvořili objekty a volali je. Na rozdíl od pracovního postupu na portálu vyžaduje přístup k kódu dostupný index pro příjem dokumentů JSON odeslaných prostřednictvím žádosti o **Vytvoření indexeru** .

Objekty blob JSON ve službě Azure Blob Storage jsou obvykle buď jedním dokumentem JSON, nebo polem JSON "Array". Indexer objektů BLOB v Azure Kognitivní hledání může analyzovat buď konstrukci v závislosti na nastavení parametru **parsingMode** v žádosti.

| Dokument JSON | parsingMode | Description | Dostupnost |
|--------------|-------------|--------------|--------------|
| Jedna na objekt BLOB | `json` | Analyzuje objekty blob JSON jako jeden blok textu. Každý objekt BLOB JSON se stal jedním dokumentem Azure Kognitivní hledání. | Obecně dostupné jak v rozhraní [REST](/rest/api/searchservice/indexer-operations) API, tak v sadě [.NET](/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Víc na objekt BLOB | `jsonArray` | Analyzuje pole JSON v objektu blob, kde se každý prvek pole stal samostatným dokumentem Azure Kognitivní hledání.  | Obecně dostupné jak v rozhraní [REST](/rest/api/searchservice/indexer-operations) API, tak v sadě [.NET](/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Víc na objekt BLOB | `jsonLines` | Analyzuje objekt blob, který obsahuje více entit JSON (pole) oddělený novým řádkem, kde se Každá entita stal samostatným dokumentem Azure Kognitivní hledání. | Obecně dostupné jak v rozhraní [REST](/rest/api/searchservice/indexer-operations) API, tak v sadě [.NET](/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1. sestavování vstupů pro požadavek

Pro každý požadavek musíte zadat název služby a klíč správce pro Azure Kognitivní hledání (v hlavičce POST) a název účtu úložiště a klíč pro úložiště objektů BLOB. K posílání požadavků HTTP do Azure Kognitivní hledání můžete použít [post](search-get-started-postman.md) .

Do poznámkového bloku zkopírujte následující čtyři hodnoty, abyste je mohli vložit do žádosti:

+ Název služby Azure Kognitivní hledání
+ Klíč správce Azure Kognitivní hledání
+ Azure storage account name
+ Klíč účtu úložiště Azure

Tyto hodnoty můžete najít na portálu:

1. Na stránkách portálu pro Azure Kognitivní hledání zkopírujte adresu URL vyhledávací služby na stránce Přehled.

2. V levém navigačním podokně klikněte na **klíče** a zkopírujte buď primární nebo sekundární klíč (jsou ekvivalentní).

3. Přepněte na stránky portálu pro váš účet úložiště. V levém navigačním podokně v části **Nastavení**klikněte na **přístupové klíče**. Tato stránka poskytuje název a klíč účtu. Zkopírujte název účtu úložiště a jeden z klíčů do poznámkového bloku.

### <a name="2---create-a-data-source"></a>2. vytvoření zdroje dat

Tento krok poskytuje informace o připojení ke zdroji dat používané indexerem. Zdroj dat je pojmenovaný objekt v Azure Kognitivní hledání, který uchovává informace o připojení. Typ zdroje dat určuje, `azureblob` které chování pro extrakci dat je vyvoláno indexerem. 

Nahraďte platné hodnoty pro název služby, klíč správce, účet úložiště a zástupné symboly pro klíč účtu.

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   
```

### <a name="3---create-a-target-search-index"></a>3. Vytvoření cílového vyhledávacího indexu 

Indexery jsou spárovány se schématem indexu. Pokud používáte rozhraní API (místo portálu), připravte index předem, abyste ho mohli zadat v operaci indexeru.

Index ukládá prohledávatelný obsah do Azure Kognitivní hledání. Chcete-li vytvořit index, zadejte schéma, které určuje pole v dokumentu, atributy a další konstrukce, které prohledají možnosti vyhledávání. Pokud vytvoříte index, který má stejné názvy polí a datových typů jako zdroj, indexer bude odpovídat zdrojovému a cílovému poli, takže vám ušetříte práci s explicitním namapováním polí.

Následující příklad ukazuje požadavek [Create index](/rest/api/searchservice/create-index) . Index bude obsahovat vyhledávací `content` pole pro uložení textu extrahovaého z objektů BLOB:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```


### <a name="4---configure-and-run-the-indexer"></a>4. konfigurace a spuštění indexeru

Stejně jako u indexu a zdroje dat je indexerem také pojmenovaný objekt, který vytvoříte a znovu použijete ve službě Azure Kognitivní hledání. Plně určený požadavek na vytvoření indexeru může vypadat takto:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }
```

Konfigurace indexeru je v těle žádosti. Vyžaduje zdroj dat a prázdný cílový index, který už existuje v Azure Kognitivní hledání. 

Parametry plánu a Parameters jsou volitelné. Pokud je vynecháte, indexer se spustí hned a použije se `json` jako režim analýzy.

Tento konkrétní indexovací člen nezahrnuje mapování polí. V definici indexeru můžete nechat **mapování polí** , pokud se vlastnosti zdrojového dokumentu JSON shodují s poli cílového vyhledávacího indexu. 


### <a name="rest-example"></a>Příklad REST

Tato část je rekapitulace všech požadavků použitých pro vytváření objektů. Diskuzi o součástech komponent najdete v předchozích částech tohoto článku.

### <a name="data-source-request"></a>Požadavek na zdroj dat

Všechny indexery vyžadují objekt zdroje dat, který poskytuje informace o připojení k existujícím datům. 

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  
```

### <a name="index-request"></a>Požadavek indexu

Všechny indexery vyžadují cílový index, který přijímá data. Tělo požadavku definuje schéma indexu, které se skládá z polí přihlášených k podpoře požadovaného chování v hledaném indexu. Tento index by měl být při spuštění indexeru prázdný. 

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

### <a name="indexer-request"></a>Požadavek indexeru

Tato žádost zobrazuje plně určeného indexer. Obsahuje mapování polí, která byla vynechána v předchozích příkladech. Odvolání, že "Schedule", "Parameters" a "fieldMappings" jsou volitelné, pokud je dostupná výchozí hodnota. Pokud vynecháte "Schedule", indexer se okamžitě spustí. Vynechání příkazu "parsingMode" způsobí, že index použije výchozí hodnotu "JSON".

Vytvoření indexeru na Azure Kognitivní hledání aktivuje import dat. Spustí se okamžitě a potom podle plánu, pokud jste ho zadali.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
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
```

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Použití sady .NET SDK

Sada .NET SDK má úplnou paritu s REST API. Doporučujeme, abyste si přečtěte předchozí část REST API, kde se dozvíte o konceptech, pracovních postupech a požadavcích. Pak se můžete podívat na následující referenční dokumentaci rozhraní .NET API a implementovat indexer JSON ve spravovaném kódu.

+ [Microsoft. Azure. Search. Models. DataSource](/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [Microsoft. Azure. Search. Models. DataSourceType](/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [Microsoft. Azure. Search. Models. index](/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [Microsoft. Azure. Search. Models. indexer](/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Režimy analýzy

Objekty blob JSON můžou předpokládat víc forem. Parametr **parsingMode** indexeru JSON určuje, jak se obsah objektu BLOB JSON analyzuje a strukturuje v indexu Azure kognitivní hledání:

| parsingMode | Description |
|-------------|-------------|
| `json`  | Indexujte každý objekt BLOB jako jeden dokument. Tato možnost je výchozí. |
| `jsonArray` | Tento režim vyberte, pokud se objekty blob skládají z polí JSON a potřebujete, aby se každý element pole stal samostatným dokumentem v Azure Kognitivní hledání. |
|`jsonLines` | Tento režim vyberte, pokud se objekty blob skládají z více entit JSON, které jsou oddělené novým řádkem, a potřebujete, aby se jednotlivé entity staly samostatným dokumentem v Azure Kognitivní hledání. |

Dokument můžete představit jako jednu položku ve výsledcích hledání. Pokud chcete, aby se všechny prvky v poli zobrazovaly ve výsledcích hledání jako nezávislá položka, použijte `jsonArray` `jsonLines` možnost nebo podle potřeby.

V rámci definice indexeru můžete volitelně použít [mapování polí](search-indexer-field-mappings.md) a vybrat, které vlastnosti zdrojového dokumentu JSON se použijí k naplnění cílového vyhledávacího indexu. V `jsonArray` případě režimu analýzy, pokud pole existuje jako vlastnost nižší úrovně, můžete nastavit kořen dokumentu, který označuje, kde je pole umístěno v objektu BLOB.

> [!IMPORTANT]
> Při použití `json` `jsonArray` nebo `jsonLines` analýze režimu Azure kognitivní hledání předpokládá, že všechny objekty blob ve zdroji dat obsahují JSON. Pokud potřebujete podporovat kombinaci objektů BLOB JSON a non-JSON ve stejném zdroji dat, dejte nám na [našem webu UserVoice](https://feedback.azure.com/forums/263029-azure-search)informace.


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Analýza jednoduchých objektů BLOB JSON

Ve výchozím nastavení služba [Azure kognitivní hledání BLOB indexer](search-howto-indexing-azure-blob-storage.md) analyzuje objekty blob JSON jako jeden blok textu. Často chcete zachovat strukturu dokumentů JSON. Předpokládejme například, že máte v úložišti objektů BLOB v Azure následující dokument JSON:

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

Indexer objektů BLOB analyzuje dokument JSON do jednoho dokumentu Azure Kognitivní hledání. Indexer Načte index spárováním "text", "datePublished" a "Tags" ze zdroje s identicky pojmenovanými a typovými poli indexu.

Jak je uvedeno, mapování polí se nevyžaduje. V případě indexu s poli "text", "datePublished a" Tags "může indexer objektů BLOB odvodit správné mapování bez mapování polí přítomného v žádosti.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analyzovat pole JSON

Alternativně můžete použít možnost pole JSON. Tato možnost je užitečná v případě, že objekty blob obsahují *pole dobře formátovaného objektu JSON*a vy chcete, aby se každý prvek stal samostatným dokumentem Azure kognitivní hledání. Například s ohledem na následující objekt BLOB JSON můžete index služby Azure Kognitivní hledání naplnit třemi samostatnými dokumenty, každý s poli "ID" a "text".  

```text
    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]
```

V případě pole JSON by definice indexeru měla vypadat podobně jako v následujícím příkladu. Všimněte si, že parametr parsingMode Určuje `jsonArray` analyzátor. Zadání správného analyzátoru a správného datového vstupu jsou jediné speciální požadavky specifické pro pole pro indexování objektů BLOB JSON.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
```

Znovu si všimněte, že mapování polí lze vynechat. Za předpokladu, že index s identicky názvem "ID" a "text" pole může indexer objektů BLOB odvodit správné mapování bez explicitního seznamu mapování polí.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Analyzovat vnořená pole
Pro pole JSON obsahující vnořené prvky lze určit, `documentRoot` aby označovala strukturu s více úrovněmi. Například pokud vaše objekty blob vypadají takto:

```http
    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }
```

Použijte tuto konfiguraci k indexování pole obsaženého ve `level2` vlastnosti:

```http
    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }
```

## <a name="parse-blobs-separated-by-newlines"></a>Analyzovat objekty blob oddělené newlines

Pokud váš objekt BLOB obsahuje více entit JSON oddělených novým řádkem a chcete, aby se každý prvek stal samostatným dokumentem Azure Kognitivní hledání, můžete se rozhodnout pro možnost řádky JSON. Například vzhledem k následujícímu objektu BLOB (kde existují tři různé entity JSON) můžete index služby Azure Kognitivní hledání naplnit třemi samostatnými dokumenty, každý s poli "ID" a "text".

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

V případě řádků JSON by definice indexeru měla vypadat podobně jako v následujícím příkladu. Všimněte si, že parametr parsingMode Určuje `jsonLines` analyzátor. 

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }
```

Znovu si všimněte, že mapování polí lze vynechat, podobně jako `jsonArray` režim analýzy.

## <a name="add-field-mappings"></a>Přidat mapování polí

Když zdrojové a cílové pole nejsou přesně zarovnané, můžete definovat oddíl mapování polí v textu žádosti pro explicitní přidružení polí do pole.

V současné době Azure Kognitivní hledání nemůže indexovat libovolné dokumenty JSON přímo, protože podporuje jenom primitivní datové typy, pole řetězců a body injson. **Mapování polí** však můžete použít k výběru částí dokumentu JSON a "výtah" do polí nejvyšší úrovně v dokumentu hledání. Další informace o základech mapování polí najdete v tématu [mapování polí v indexerech Azure kognitivní hledání](search-indexer-field-mappings.md).

Přečtěte si náš ukázkový dokument JSON:

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

Vybere index vyhledávání s následujícími poli: `text` typu `Edm.String` , `date` typu `Edm.DateTimeOffset` a `tags` typu `Collection(Edm.String)` . Všimněte si rozdílů mezi "datePublished" ve zdroji a `date` v poli v indexu. K namapování JSON na požadovaný tvar použijte následující mapování polí:

```http
    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]
```

Názvy zdrojových polí v mapování jsou určeny pomocí zápisu [ukazatele JSON](https://tools.ietf.org/html/rfc6901) . Začínáte s dopředným lomítkem, kde odkazujete na kořen dokumentu JSON, a pak vyberte požadovanou vlastnost (na libovolné úrovni vnoření) pomocí cesty oddělené lomítkem.

Můžete také odkazovat na jednotlivé prvky pole pomocí indexu založeného na nule. Chcete-li například vybrat první prvek pole "značky" z výše uvedeného příkladu, použijte mapování polí takto:

```http
    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Pokud název zdrojového pole v cestě mapování polí odkazuje na vlastnost, která neexistuje ve formátu JSON, toto mapování je vynecháno bez chyby. K tomu je potřeba, abychom mohli podporovat dokumenty s jiným schématem (což je běžný případ použití). Vzhledem k tomu, že není k dispozici žádné ověření, je třeba dbát na to, abyste zabránili překlepům ve specifikaci mapování polí.
>
>

## <a name="see-also"></a>Viz také

+ [Indexery ve službě Azure Cognitive Search](search-indexer-overview.md)
+ [Indexování služby Azure Blob Storage s využitím Azure Kognitivní hledání](search-howto-index-json-blobs.md)
+ [Indexování objektů BLOB CSV pomocí indexeru Azure Kognitivní hledání BLOB](search-howto-index-csv-blobs.md)
+ [Kurz: hledání částečně strukturovaných dat z Azure Blob Storage](search-semi-structured-data.md)