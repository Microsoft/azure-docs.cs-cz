---
title: 'Kurz: Indexujte částečně strukturovaná data v objektech BLOB JSON'
titleSuffix: Azure Cognitive Search
description: Zjistěte, jak indexovat a vyhledávat částečně strukturované objekty BLOB Azure JSON pomocí azure cognitive search REST API a Postman.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: ce3b3839319de38020b968ff8db1ee6713b29c47
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78269981"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Kurz: Indexovat objekty BLOB JSON z Azure Storage pomocí REST

Azure Cognitive Search můžete indexovat Dokumenty JSON a pole v úložišti objektů blob Azure pomocí [indexeru,](search-indexer-overview.md) který ví, jak číst částečně strukturovaná data. Částečně strukturovaná data obsahují značky nebo označení oddělující obsah v rámci dat. Rozdělí rozdíl mezi nestrukturovaná data, která musí být plně indexována, a formálně strukturovaná data, která se drží datového modelu, jako je například schéma relační databáze, které lze indexovat na základě pole.

Tento kurz používá Postman a [hledání REST API](https://docs.microsoft.com/rest/api/searchservice/) provádět následující úkoly:

> [!div class="checklist"]
> * Konfigurace zdroje dat Azure Cognitive Search pro kontejner objektů blob Azure
> * Vytvoření indexu Azure Cognitive Search, který bude obsahovat prohledávatelný obsah
> * Konfigurace a spuštění indexeru pro čtení kontejneru a extrahování prohledávatelného obsahu z úložiště objektů blob Azure
> * Prohledávání právě vytvořeného indexu

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

+ [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Desktopová aplikace Postman](https://www.getpostman.com/)
+ [Vytvoření](search-create-service-portal.md) nebo [vyhledání existující vyhledávací služby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Můžete použít bezplatnou službu pro tento kurz. Bezplatná vyhledávací služba vás omezí na tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Než začnete, ujistěte se, že máte ve službě prostor pro přijetí nových zdrojů.

## <a name="download-files"></a>Stažení souborů

[Klinické zkoušky-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) obsahuje data použitá v tomto kurzu. Stáhněte a rozbalte tento soubor do vlastní složky. Data pocházejí z [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), převedeny na JSON pro tento kurz.

## <a name="1---create-services"></a>1 - Vytváření služeb

Tento kurz používá Azure Cognitive Search pro indexování a dotazy a úložiště objektů blob Azure k poskytování dat. 

Pokud je to možné, vytvořte ve stejné oblasti a skupině prostředků pro blízkost a správu. V praxi může být váš účet Azure Storage v libovolné oblasti.

### <a name="start-with-azure-storage"></a>Začněte s Azure Storage

1. [Přihlaste se na portál Azure](https://portal.azure.com/) a klikněte na + Vytvořit **prostředek**.

1. Vyhledejte *účet úložiště* a vyberte nabídku účtu úložiště společnosti Microsoft.

   ![Vytvořit účet úložiště](media/cognitive-search-tutorial-blob/storage-account.png "Vytvořit účet úložiště")

1. Na kartě Základy jsou požadovány následující položky. Přijměte výchozí hodnoty pro všechno ostatní.

   + **Skupina prostředků**. Vyberte existující nebo vytvořte novou, ale použijte stejnou skupinu pro všechny služby, abyste je mohli spravovat společně.

   + **Název účtu úložiště**. Pokud si myslíte, že můžete mít více prostředků stejného typu, použijte název k oddělení podle typu a oblasti, například *blobstoragewestus*. 

   + **Umístění**. Pokud je to možné, zvolte stejné umístění, které se používá pro Azure Cognitive Search a Cognitive Services. Jedno místo ruší poplatky za šířku pásma.

   + **Typ účtu**. Zvolte výchozí, *StorageV2 (pro obecné účely v2)*.

1. Chcete-li službu vytvořit, klepněte na **tlačítko Revize + Vytvořit.**

1. Po jeho vytvoření kliknutím na **Přejít na zdroj** otevřete stránku Přehled.

1. Klikněte na **službu Objektů blob.**

1. [Vytvořte kontejner objektů blob,](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) který bude obsahovat ukázková data. Úroveň veřejného přístupu můžete nastavit na libovolnou z jejích platných hodnot.

1. Po vytvoření kontejneru ho otevřete a na panelu příkazů vyberte **Nahrát.**

   ![Nahrát na panelu příkazů](media/search-semi-structured-data/upload-command-bar.png "Nahrát na panelu příkazů")

1. Přejděte do složky obsahující ukázkové soubory. Vyberte všechny a klepněte na tlačítko **Nahrát**.

   ![Nahrání souborů](media/search-semi-structured-data/clinicalupload.png "Nahrání souborů")

Po dokončení nahrávání by se soubory měly zobrazit v samostatné podsložce uvnitř kontejneru dat.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Dalším prostředkem je Azure Cognitive Search, který můžete [vytvořit na portálu](search-create-service-portal.md). K dokončení tohoto návodu můžete použít úroveň Free. 

Stejně jako u úložiště objektů Blob Azure, věnujte chvilku shromažďování přístupového klíče. Dále, když začnete strukturovat požadavky, budete muset poskytnout koncový bod a klíč rozhraní api správce, který se používá k ověření každého požadavku.

### <a name="get-a-key-and-url"></a>Získání klíče a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do předplatného přidali Azure Cognitive Search, postupujte podle následujících kroků a získejte potřebné informace:

1. [Přihlaste se na portál Azure](https://portal.azure.com/)portal a na stránce **Přehled** vyhledávací služby získáte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V **nastavení** > **klíče**, získat klíč správce pro úplná práva ke službě. Existují dva zaměnitelné klíče pro správu, které jsou k dispozici pro kontinuitu podnikání v případě, že potřebujete převrátit jeden. Primární nebo sekundární klíč můžete použít při požadavcích na přidávání, úpravy a odstranění objektů.

![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní api na každý požadavek odeslaný do vaší služby. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="2---set-up-postman"></a>2 - Nastavit pošťáka

Spusťte Postman a nastavte požadavek HTTP. Pokud nejste obeznámeni s tímto nástrojem, najdete [v tématu prozkoumat Azure Cognitive Search REST API pomocí Postman](search-get-started-postman.md).

Metody požadavku pro každé volání v tomto kurzu jsou **POST** a **GET**. Provedete tři volání rozhraní API do vyhledávací služby k vytvoření zdroje dat, indexu a indexeru. Zdroj dat obsahuje ukazatel na váš účet úložiště a vaše data JSON. Vaše služba Search se připojí při načítání dat.

V záhlaví nastavte "Typ obsahu" `application/json` a `api-key` nastavte na klíč rozhraní api správce vaší služby Azure Cognitive Search. Jakmile nastavíte záhlaví, můžete je použít pro každý požadavek v tomto cvičení.

  ![Adresa URL a záhlaví požadavku pošťáka](media/search-get-started-postman/postman-url.png "Adresa URL a záhlaví požadavku pošťáka")

Identifikátory URI musí zadat verzi rozhraní api a každé volání by mělo vrátit **201 Created**. Obecně dostupná verze rozhraní API pro použití `2019-05-06`polí JSON je .

## <a name="3---create-a-data-source"></a>3 - Vytvoření zdroje dat

Vytvořit [rozhraní API zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) vytvoří objekt Azure Cognitive Search, který určuje, jaká data mají být indexována.

1. Nastavte koncový bod tohoto `https://[service name].search.windows.net/datasources?api-version=2019-05-06`volání na . Nahraďte `[service name]` názvem vaší služby Search. 

1. Zkopírujte následující JSON do těla požadavku.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. Nahraďte připojovací řetězec platným řetězcem pro váš účet.

1. Nahraďte "[název kontejneru objektu blob]" kontejnerem, který jste vytvořili pro ukázková data. 

1. Odešlete požadavek. Odpověď by měla vypadat nějak takto:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
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

## <a name="4---create-an-index"></a>4 - Vytvoření indexu
    
Druhé volání je [vytvořit index rozhraní API](https://docs.microsoft.com/rest/api/searchservice/create-index), vytvoření indexu Azure Cognitive Search, který ukládá všechna prohledávatelná data. Index určuje všechny parametry a jejich atributy.

1. Nastavte koncový bod tohoto `https://[service name].search.windows.net/indexes?api-version=2019-05-06`volání na . Nahraďte `[service name]` názvem vaší služby Search.

1. Zkopírujte následující JSON do těla požadavku.

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
      ]
    }
   ```

1. Odešlete požadavek. Odpověď by měla vypadat nějak takto:

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
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5 - Vytvoření a spuštění indexeru

Indexer se připojí ke zdroji dat, importuje data do cílového indexu vyhledávání a volitelně poskytuje plán pro automatizaci aktualizace dat. Rozhraní REST API je [vytvořit indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

1. Nastavte identifikátor URI pro `https://[service name].search.windows.net/indexers?api-version=2019-05-06`toto volání na . Nahraďte `[service name]` názvem vaší služby Search.

1. Zkopírujte následující JSON do těla požadavku.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. Odešlete požadavek. Požadavek je zpracován okamžitě. Když se odpověď vrátí, budete mít index, který lze prohledávat jako fulltextový. Odpověď by měla vypadat nějak takto:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
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

## <a name="6---search-your-json-files"></a>6 - Vyhledávání souborů JSON

Hledání můžete začít ihned po načtení prvního dokumentu.

1. Změňte sloveso na **GET**.

1. Nastavte identifikátor URI pro `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2019-05-06&$count=true`toto volání na . Nahraďte `[service name]` názvem vaší služby Search.

1. Odešlete požadavek. Toto je nespecifikovaný fulltextový vyhledávací dotaz, který vrací všechna pole označená jako načítatelná v indexu spolu s počtem dokumentů. Odpověď by měla vypadat nějak takto:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. Přidáním `$select` parametru dotazu omezíte `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2019-05-06&$count=true`výsledky na méně polí: .  Pro tento dotaz 100 dokumentů odpovídá, ale ve výchozím nastavení Azure Cognitive Search vrátí jenom 50 ve výsledcích.

   ![Parameterizovaný dotaz](media/search-semi-structured-data/lastquery.png "Paramterizovaný dotaz")

1. Příklad složitější dotaz by `$filter=MinimumAge ge 30 and MaximumAge lt 75`patří , který vrátí pouze výsledky, kde parametry MinimumAge je větší nebo rovno 30 a MaximumAge je menší než 75. Nahraďte `$select` výraz `$filter` výrazem.

   ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/metadatashort.png)

Můžete také použít logické operátory (a nebo ne) a operátory porovnání (eq, ne, gt, lt, ge, le). Při porovnávání řetězců se rozlišují malá a velká písmena. Další informace a příklady naleznete [v tématu Vytvoření jednoduchého dotazu](search-query-simple-examples.md).

> [!NOTE]
> Parametr `$filter` pracuje pouze s metadaty, která se při vytváření indexu označila jako filtrovatelná.

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

V raných experimentálních fázích vývoje je nejpraktičtějším přístupem pro iteraci návrhu odstranění objektů z Azure Cognitive Search a povolení jejich opětovného sestavení kódu. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Portál můžete použít k odstranění indexů, indexerů a zdrojů dat. Nebo použijte **DELETE** a poskytněte adresy URL každému objektu. Následující příkaz odstraní indexer.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2019-05-06
```

Při úspěšném odstranění se vrátí kód stavu 204.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Při práci ve vlastním předplatném je na konci projektu vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu Všechny prostředky nebo skupiny prostředků v levém navigačním podokně.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni se základy indexování objektů blob Azure, podívejme se blíže na konfiguraci indexeru pro objekty BLOB JSON ve službě Azure Storage.

> [!div class="nextstepaction"]
> [Konfigurace indexování objektů blob JSON](search-howto-index-json-blobs.md)