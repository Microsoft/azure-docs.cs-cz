---
title: Kurz pro hledání JSON ve službě Azure Blob storage – Azure Search
description: V tomto kurzu se dozvíte, jak prohledávat částečně strukturovaná data objektů blob Azure pomocí služby Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 1c8ce14dd3961eff33a54a14c2bd0b27650d8a50
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201343"
---
# <a name="tutorial-search-semi-structured-data-in-azure-cloud-storage"></a>Kurz: Prohledávání částečně strukturovaných dat v cloudovém úložišti Azure

Služba Azure Search můžete indexovat dokumenty JSON a polí v úložiště objektů blob v Azure pomocí [indexer](search-indexer-overview.md) , který umí číst částečně strukturovaná data. Částečně strukturovaná data obsahují značky nebo označení oddělující obsah v rámci dat. Rozdělí rozdíl mezi nestrukturovaných dat, která musí být plně indexovat a formálně strukturovaná data, která dodržuje datový model, jako je schéma relační databáze, které můžete indexovat na základě na pole.

V tomto kurzu, používá [rozhraní REST API Azure Search](https://docs.microsoft.com/rest/api/searchservice/) a klienta REST k provádění následujících úloh:

> [!div class="checklist"]
> * Konfigurace zdroje dat Azure Search pro kontejner objektů blob Azure
> * Vytvoření indexu Azure Search tak, aby obsahovala prohledávatelného obsahu
> * Nakonfigurování a spuštění indexeru ke čtení kontejneru a extrahování prohledávatelného obsahu z Azure blob storage
> * Prohledávání právě vytvořeného indexu

> [!NOTE]
> Tento kurz se spoléhá na podporu polí JSON, která je aktuálně ve službě Azure Search funkcí ve verzi Preview. Na portálu není k dispozici. Z tohoto důvodu používáme rozhraní REST API verze Preview, které tuto funkci poskytuje, a klientský nástroj REST k volání tohoto rozhraní API.

## <a name="prerequisites"></a>Požadavky

[Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službu pro účely tohoto kurzu.

[Vytvoření účtu služby Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) obsahuje ukázková data.

[Pomocí nástroje Postman](https://www.getpostman.com/) nebo jiného klienta REST k odesílání požadavků. Pokyny pro nastavení požadavku HTTP v nástroji Postman jsou k dispozici v další části.

## <a name="set-up-postman"></a>Nastavení nástroje Postman

Spusťte Postman a nastavte požadavek HTTP. Pokud tento nástroj neznáte, přečtěte si téma [prozkoumejte službu Search REST API služby Azure pomocí nástroje Postman](search-fiddler.md).

Metoda požadavku pro všechna volání v tomto kurzu je POST. Klíče hlaviček jsou Content-type a api-key. Hodnoty těchto klíčů hlaviček jsou application/json a váš klíč správce (klíč správce je zástupná hodnota za váš primární klíč služby Search). Samotný obsah volání se vkládá do textu požadavku. V závislosti na klientovi, kterého používáte, se může způsob vytváření dotazu mírně lišit, ale toto jsou základní informace.

  ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/postmanoverview.png)

Volání REST uvedená v tomto kurzu vyžadují váš api-key (klíč rozhraní API) služby Search. Svůj api-key najdete v části **Klíče** v rámci vaší služby Search. Tento api-key musí být uvedený v hlavičce všech volání rozhraní API (nahraďte jím klíč správce na předchozím snímku obrazovky), která v rámci tohoto kurzu provedete. Klíč si uložte, protože ho budete potřebovat pro všechna volání.

  ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/keys.png)

## <a name="prepare-sample-data"></a>Příprava ukázkových dat

1. **Stáhněte soubor [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)** a rozbalte ho do samostatné složky. Data pocházejí z [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), převést na JSON pro účely tohoto kurzu.

2. Přihlaste se k [webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště Azure, otevřete **data** kontejneru a klikněte na **nahrát**.

3. Klikněte na **Upřesnit**, zadejte clinical-trials-json a pak nahrajte všechny soubory JSON, které jste stáhli.

  ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/clinicalupload.png)

Po dokončení nahrávání by se soubory měly zobrazit v samostatné podsložce uvnitř kontejneru dat.

## <a name="connect-your-search-service-to-your-container"></a>Propojení služby Search s kontejnerem

Pomocí nástroje Postman provedeme tři volání rozhraní API do služby Search, kterými vytvoříme zdroj dat, index a indexer. Zdroj dat obsahuje ukazatel na váš účet úložiště a vaše data JSON. Vaše služba Search se připojí při načítání dat.

Řetězec dotazu musí obsahovat verze preview rozhraní API (například **verze api-version = 2017-11-11-Preview**) a každé volání by mělo vrátit **201 – vytvořeno**. Obecně dostupná verze rozhraní API ještě neumožňuje zpracovávat JSON jako pole JSON. V současné době to umožňuje pouze rozhraní API ve verzi Preview.

Ve svém klientovi REST proveďte následující tři volání rozhraní API.

## <a name="create-a-data-source"></a>Vytvoření zdroje dat

Zdroj dat je objekt, který určuje, jaká data do indexu Azure Search.

Koncový bod tohoto volání je `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Nahraďte `[service name]` názvem vaší služby Search. Pro toto volání potřebujete název a klíč vašeho účtu úložiště. Klíč účtu úložiště najdete na webu Azure Portal v části **Přístupové klíče** vašeho účtu úložiště. Umístění je znázorněné na následujícím obrázku:

  ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/storagekeys.png)

Než provedete volání, nezapomeňte nahradit `[storage account name]` a `[storage account key]` v jeho textu.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
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
        "name": "data",
        "query": "clinical-trials-json"
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Vytvoření indexu
    
Druhé volání rozhraní API vytvoří index Azure Search. Index určuje všechny parametry a jejich atributy.

Adresa URL pro toto volání je `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`. Nahraďte `[service name]` názvem vaší služby Search.

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

Indexer propojuje zdroj dat, naimportuje data do cílovým indexem vyhledávání a volitelně poskytuje plán pro automatizaci aktualizace dat.

Adresa URL pro toto volání je `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Nahraďte `[service name]` názvem vaší služby Search.

Nejprve nahraďte adresu URL. Pak zkopírujte a vložte následující kód do textu vašeho požadavku a odeslat žádost. Požadavek je zpracován okamžitě. Po odpověď se vrátí zpět, budete mít index, který je fulltextově prohledávatelné.

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

Nyní můžete zadávat dotazy na index. Pro tento úkol použijte [ **Průzkumníka služby Search** ](search-explorer.md) na portálu.

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

Algoritmy využívající AI je možné připojit ke kanálu indexování. Jako další krok pokračujte následujícím kurzem.

> [!div class="nextstepaction"]
> [Indexování dokumentů ve službě Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)