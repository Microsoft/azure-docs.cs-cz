---
title: 'Kurz REST: Indexování částečně strutured dat v objektech blob JSON – Azure Search'
description: Naučte se indexovat a prohledávat částečně strukturované objekty blob služby Azure JSON pomocí Azure Search rozhraní REST API a post.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: cb9c97efd62a56ad0eac49956f11fb422a448194
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647860"
---
# <a name="rest-tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-search"></a>Kurz REST: Indexujte a prohledejte částečně strukturovaná data (bloby JSON) v Azure Search

Azure Search může indexovat dokumenty a pole JSON ve službě Azure Blob Storage [](search-indexer-overview.md) pomocí indexeru, který ví, jak číst částečně strukturovaná data. Částečně strukturovaná data obsahují značky nebo označení oddělující obsah v rámci dat. Rozdělí rozdíl mezi nestrukturovanými daty, která musí být plně indexována, a formálně strukturovaná data, která jsou v datovém modelu, například ve schématu relační databáze, která lze indexovat podle jednotlivých polí.

V tomto kurzu můžete pomocí [rozhraní REST api Azure Search](https://docs.microsoft.com/rest/api/searchservice/) a klienta REST provádět následující úlohy:

> [!div class="checklist"]
> * Konfigurace zdroje dat Azure Search pro kontejner objektů blob Azure
> * Vytvoření indexu Azure Search, který bude obsahovat prohledávatelný obsah
> * Konfigurace a spuštění indexeru pro čtení kontejneru a extrakce vyhledávaného obsahu z úložiště objektů BLOB v Azure
> * Prohledávání právě vytvořeného indexu

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu se používají následující služby, nástroje a data. 

[Vytvořte službu Azure Search](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento kurz můžete použít bezplatnou službu. 

[Vytvořte účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pro ukládání ukázkových dat.

[Aplikace po pracovní ploše](https://www.getpostman.com/) pro odesílání požadavků na Azure Search.

[Clinical-Trials-JSON. zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) obsahuje data použitá v tomto kurzu. Stáhnout a rozbalit tento soubor do vlastní složky. Data pocházejí z [ClinicalTrials.gov](https://clinicaltrials.gov/ct2/results), která jsou pro tento kurz převedená na JSON.

## <a name="get-a-key-and-url"></a>Získat klíč a adresu URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. Přihlaste se [k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení** > **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

![Získání koncového bodu http a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu http a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="prepare-sample-data"></a>Příprava ukázkových dat

1. Přihlaste se [k Azure Portal](https://portal.azure.com), přejděte k účtu úložiště Azure, klikněte na **objekty blob**a pak klikněte na **+ kontejner**.

1. [Vytvořte kontejner objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) , který bude obsahovat vzorová data. Úroveň veřejného přístupu můžete nastavit na libovolnou z jeho platných hodnot.

1. Po vytvoření kontejneru ho otevřete a na panelu příkazů vyberte **nahrát** .

   ![Nahrát na panel příkazů](media/search-semi-structured-data/upload-command-bar.png "Nahrát na panel příkazů")

1. Přejděte do složky, která obsahuje ukázkové soubory. Vyberte všechny z nich a pak klikněte na **nahrát**.

   ![Nahrání souborů](media/search-semi-structured-data/clinicalupload.png "Nahrání souborů")

Po dokončení nahrávání by se soubory měly zobrazit v samostatné podsložce uvnitř kontejneru dat.

## <a name="set-up-postman"></a>Nastavení nástroje Postman

Spusťte Postman a nastavte požadavek HTTP. Pokud tento nástroj neznáte, přečtěte si téma [zkoumání Azure Search rozhraní REST API pomocí post](search-get-started-postman.md).

Metoda žádosti pro každé volání v tomto kurzu je **post**. Klíče hlaviček jsou Content-type a api-key. Hodnoty těchto klíčů hlaviček jsou application/json a váš klíč správce (klíč správce je zástupná hodnota za váš primární klíč služby Search). Samotný obsah volání se vkládá do textu požadavku. V závislosti na klientovi, kterého používáte, se může způsob vytváření dotazu mírně lišit, ale toto jsou základní informace.

  ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/postmanoverview.png)

Pomocí nástroje Postman provedeme tři volání rozhraní API do služby Search, kterými vytvoříme zdroj dat, index a indexer. Zdroj dat obsahuje ukazatel na váš účet úložiště a vaše data JSON. Vaše služba Search se připojí při načítání dat.

Řetězce dotazů musí určovat verzi rozhraní API a každé volání by mělo vrátit **vytvořené 201**. Obecně dostupná verze API-Version pro použití polí JSON je `2019-05-06`.

Ve svém klientovi REST proveďte následující tři volání rozhraní API.

## <a name="create-a-data-source"></a>Vytvoření zdroje dat

[Rozhraní API pro vytvoření zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source)vytvoří objekt Azure Search, který určuje, jaká data se mají indexovat.

Koncový bod tohoto volání je `https://[service name].search.windows.net/datasources?api-version=2019-05-06`. Nahraďte `[service name]` názvem vaší služby Search. 

V případě tohoto volání musí tělo žádosti zahrnovat název vašeho účtu úložiště, klíč účtu úložiště a název kontejneru objektů BLOB. Klíč účtu úložiště najdete na webu Azure Portal v části **Přístupové klíče** vašeho účtu úložiště. Umístění je znázorněné na následujícím obrázku:

  ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/storagekeys.png)

Před provedením volání `[storage account name]`nezapomeňte `[storage account key]`nahradit, `[blob container name]` a v těle volání.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "[blob container name]"}
}
```

Odpověď by měla vypadat nějak takto:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "[mycontainernamehere]",
        "query": null
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Vytvoření indexu
    
Druhé volání je [vytvořit index rozhraní API](https://docs.microsoft.com/rest/api/searchservice/create-indexer)a vytvoří Azure Search index, který ukládá všechna hledaná data. Index určuje všechny parametry a jejich atributy.

Adresa URL pro toto volání je `https://[service name].search.windows.net/indexes?api-version=2019-05-06`. Nahraďte `[service name]` názvem vaší služby Search.

Nejprve nahraďte adresu URL. Pak zkopírujte následující kód, vložte ho do textu vašeho požadavku a spusťte dotaz.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

Odpověď by měla vypadat nějak takto:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

## <a name="create-and-run-an-indexer"></a>Vytvoření a spuštění indexeru

Indexer připojuje zdroj dat, importuje data do cílového vyhledávacího indexu a volitelně poskytuje plán pro automatizaci aktualizace dat. REST API je [vytvořit indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Adresa URL pro toto volání je `https://[service name].search.windows.net/indexers?api-version=2019-05-06`. Nahraďte `[service name]` názvem vaší služby Search.

Nejprve nahraďte adresu URL. Pak zkopírujte a vložte následující kód do svého těla a odešlete žádost. Požadavek se okamžitě zpracuje. Až se odpověď vrátí zpět, budete mít index, který bude fulltextově prohledávatelné.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

Odpověď by měla vypadat nějak takto:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>Prohledávání souborů JSON

Hledání můžete zahájit hned po načtení prvního dokumentu. Pro tuto úlohu použijte [**Průzkumníka služby Search**](search-explorer.md) na portálu.

V Azure Portal otevřete stránku **Přehled** služby Search, najděte index, který jste vytvořili v seznamu **indexy** .

Nezapomeňte zvolit index, který jste právě vytvořili. 

  ![Prohledávání nestrukturovaných dat](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Prohledávání metadat definovaných uživatelem

Stejně jako předtím je možné data dotazovat několika způsoby: fulltextové vyhledávání, systémové vlastnosti nebo metadata definovaná uživatelem. Systémové vlastnosti a metadata definovaná uživatelem je možné prohledávat pouze s použitím parametru `$select`, pokud se pro ně při vytváření cílového indexu použilo označení **retrievable**. Parametry v indexu není možné po vytvoření změnit. Můžete však přidat další parametry.

Příkladem základního dotazu je `$select=Gender,metadata_storage_size`, který omezí vrácené výsledky na dané dva parametry.

  ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/lastquery.png)

Příkladem složitějšího dotazu je `$filter=MinimumAge ge 30 and MaximumAge lt 75`, který vrátí pouze výsledky, u kterých je hodnota parametru MinimumAge větší nebo rovna 30 a hodnota parametru MaximumAge je menší než 75.

  ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/metadatashort.png)

Pokud chcete, můžete experimentovat a vyzkoušet si sami několik dalších dotazů. Mějte na paměti, že můžete používat logické operátory (and, or, not) a porovnávací operátory (eq, ne, gt, lt, ge, le). Při porovnávání řetězců se rozlišují malá a velká písmena.

Parametr `$filter` pracuje pouze s metadaty, která se při vytváření indexu označila jako filtrovatelná.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejším způsobem, jak po kurzu všechno uklidit, je odstranit skupinu prostředků, která obsahuje službu Azure Search. Odstraněním skupiny prostředků teď můžete trvale odstranit všechno, co se v ní nachází. Název příslušné skupiny prostředků najdete na portálu na stránce Přehled služby Azure Search.

## <a name="next-steps"></a>Další postup

Existuje několik přístupů a několik možností indexování objektů BLOB JSON. V dalším kroku si můžete projít a otestovat různé možnosti, abyste viděli, co nejlépe vyhovuje vašemu scénáři.

> [!div class="nextstepaction"]
> [Postup indexování objektů BLOB JSON pomocí Azure Search indexeru objektů BLOB](search-howto-index-json-blobs.md)
