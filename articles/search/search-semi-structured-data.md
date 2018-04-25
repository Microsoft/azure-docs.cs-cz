---
title: Prohledávání částečně strukturovaných dat v cloudovém úložišti Azure
description: Prohledávání částečně strukturovaných dat objektů blob pomocí služby Azure Search
author: roygara
manager: cgronlun
ms.service: search
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: v-rogara
ms.openlocfilehash: f05e9dd12a838199b23deddb4f6c4fb4c2fced08
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="part-2-search-semi-structured-data-in-cloud-storage"></a>Část 2: Prohledávání částečně strukturovaných dat v cloudovém úložišti

V této dvoudílné sérii kurzů se dozvíte, jak pomocí služby Azure Search prohledávat částečně strukturovaná a nestrukturovaná data. [Část 1](../storage/blobs/storage-unstructured-search.md) vás provedla prohledáváním nestrukturovaných dat, ale obsahovala také důležité požadavky pro tento kurz, například vytvoření účtu úložiště. 

V části 2 se pozornost zaměří na částečně strukturovaná data, jako je JSON, uložená v objektech blob Azure. Částečně strukturovaná data obsahují značky nebo označení oddělující obsah v rámci dat. Představují kompromis mezi nestrukturovanými daty, která je nutné indexovat jako celek, a formálně strukturovanými daty, která se řídí datovým modelem, jako je schéma relační databáze, a která je možné procházet po jednotlivých polích.

V části 2 získáte informace o těchto tématech:

> [!div class="checklist"]
> * Konfigurace zdroje dat Azure Search pro kontejner objektů blob Azure
> * Vytvoření a naplnění indexu a indexeru Azure Search pro procházení kontejneru a extrahování prohledávatelného obsahu
> * Prohledávání právě vytvořeného indexu

> [!NOTE]
> Tento kurz se spoléhá na podporu polí JSON, která je aktuálně ve službě Azure Search funkcí ve verzi Preview. Na portálu není k dispozici. Z tohoto důvodu používáme rozhraní REST API verze Preview, které tuto funkci poskytuje, a klientský nástroj REST k volání tohoto rozhraní API.

## <a name="prerequisites"></a>Požadavky

* Dokončení [předchozího kurzu](../storage/blobs/storage-unstructured-search.md) a vytvoření účtu úložiště a služby Search.

* Instalace klienta REST a porozumění vytváření požadavků HTTP. Pro účely tohoto kurzu používáme nástroj [Postman](https://www.getpostman.com/). Můžete použít i jiného klienta REST, pokud se vám už s některým dobře pracuje.

## <a name="set-up-postman"></a>Nastavení nástroje Postman

Spusťte Postman a nastavte požadavek HTTP. Pokud tento nástroj neznáte, přečtěte si další informace v tématu [Zkoumání rozhraní REST API služby Azure Search pomocí nástroje Fiddler nebo Postman](search-fiddler.md).

Metoda požadavku pro všechna volání v tomto kurzu je POST. Klíče hlaviček jsou Content-type a api-key. Hodnoty těchto klíčů hlaviček jsou application/json a váš klíč správce (klíč správce je zástupná hodnota za váš primární klíč služby Search). Samotný obsah volání se vkládá do textu požadavku. V závislosti na klientovi, kterého používáte, se může způsob vytváření dotazu mírně lišit, ale toto jsou základní informace.

  ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/postmanoverview.png)

Volání REST uvedená v tomto kurzu vyžadují váš api-key (klíč rozhraní API) služby Search. Svůj api-key najdete v části **Klíče** v rámci vaší služby Search. Tento api-key musí být uvedený v hlavičce všech volání rozhraní API (nahraďte jím klíč správce na předchozím snímku obrazovky), která v rámci tohoto kurzu provedete. Klíč si uložte, protože ho budete potřebovat pro všechna volání.

  ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/keys.png)

## <a name="download-the-sample-data"></a>Stažení ukázkových dat

Připravili jsme pro vás ukázkovou datovou sadu. **Stáhněte soubor [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)** a rozbalte ho do samostatné složky.

Ukázka obsahuje příklady souborů JSON, které byly původně textové soubory získané z webu [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Pro usnadnění práce jsme je převedli do formátu JSON.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k portálu [Azure Portal](http://portal.azure.com).

## <a name="upload-the-sample-data"></a>Nahrání ukázkových dat

Na webu Azure Portal se vraťte do účtu úložiště vytvořeného v [předchozím kurzu](../storage/blobs/storage-unstructured-search.md). Pak otevřete kontejner **data** a klikněte na **Nahrát**.

Klikněte na **Upřesnit**, zadejte clinical-trials-json a pak nahrajte všechny soubory JSON, které jste stáhli.

  ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/clinicalupload.png)

Po dokončení nahrávání by se soubory měly zobrazit v samostatné podsložce uvnitř kontejneru dat.

## <a name="connect-your-search-service-to-your-container"></a>Propojení služby Search s kontejnerem

Pomocí nástroje Postman provedeme tři volání rozhraní API do služby Search, kterými vytvoříme zdroj dat, index a indexer. Zdroj dat obsahuje ukazatel na váš účet úložiště a vaše data JSON. Vaše služba Search se připojí při načítání dat.

Řetězec dotazu musí obsahovat **api-version=2016-09-01-Preview** a každé volání by mělo vracet stavový kód **201 Vytvořeno**. Obecně dostupná verze rozhraní API ještě neumožňuje zpracovávat JSON jako pole JSON. V současné době to umožňuje pouze rozhraní API ve verzi Preview.

Ve svém klientovi REST proveďte následující tři volání rozhraní API.

### <a name="create-a-datasource"></a>Vytvoření zdroje dat

Zdroj dat určuje, která data se mají indexovat.

Koncový bod tohoto volání je `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Nahraďte `[service name]` názvem vaší služby Search.

Pro toto volání potřebujete název a klíč vašeho účtu úložiště. Klíč účtu úložiště najdete na webu Azure Portal v části **Přístupové klíče** vašeho účtu úložiště. Umístění je znázorněné na následujícím obrázku:

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

### <a name="create-an-index"></a>Vytvoření indexu
    
Druhé volání rozhraní API vytvoří index. Index určuje všechny parametry a jejich atributy.

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

### <a name="create-an-indexer"></a>Vytvoření indexeru

Indexer propojuje zdroj dat s cílovým indexem vyhledávání a volitelně poskytuje plán pro automatizaci aktualizace dat.

Adresa URL pro toto volání je `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Nahraďte `[service name]` názvem vaší služby Search.

Nejprve nahraďte adresu URL. Pak zkopírujte následující kód, vložte ho do textu vašeho požadavku a spusťte dotaz.

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

Teď, když je vaše služba Search propojená s vaším kontejnerem dat, můžete začít prohledávat soubory.

Otevřete Azure Portal a vraťte se do svojí služby Search. Postupujte stejně jako v předchozím kurzu.

  ![Prohledávání nestrukturovaných dat](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Prohledávání metadat definovaných uživatelem

Stejně jako předtím je možné data dotazovat několika způsoby: fulltextové vyhledávání, systémové vlastnosti nebo metadata definovaná uživatelem. Systémové vlastnosti a metadata definovaná uživatelem je možné prohledávat pouze s použitím parametru `$select`, pokud se pro ně při vytváření cílového indexu použilo označení **retrievable**. Parametry v indexu není možné po vytvoření změnit. Můžete však přidat další parametry.

Příkladem základního dotazu je `$select=Gender,metadata_storage_size`, který omezí vrácené výsledky na dané dva parametry.

  ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/lastquery.png)

Příkladem složitějšího dotazu je `$filter=MinimumAge ge 30 and MaximumAge lt 75`, který vrátí pouze výsledky, u kterých je hodnota parametru MinimumAge větší nebo rovna 30 a hodnota parametru MaximumAge je menší než 75.

  ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/metadatashort.png)

Pokud chcete, můžete experimentovat a vyzkoušet si sami několik dalších dotazů. Mějte na paměti, že můžete používat logické operátory (and, or, not) a porovnávací operátory (eq, ne, gt, lt, ge, le). Při porovnávání řetězců se rozlišují malá a velká písmena.

Parametr `$filter` pracuje pouze s metadaty, která se při vytváření indexu označila jako filtrovatelná.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili s prohledáváním částečně strukturovaných dat pomocí služby Azure Search a následujícími tématy:

> [!div class="checklist"]
> * Vytvoření služby Azure Search pomocí rozhraní REST API
> * Použití služby Azure Search k prohledávání kontejneru

Další informace o vyhledávání najdete na následujícím odkazu.

> [!div class="nextstepaction"]
> [Indexování dokumentů ve službě Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)