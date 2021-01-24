---
title: 'Kurz: indexování částečně strukturovaných dat v objektech blob JSON'
titleSuffix: Azure Cognitive Search
description: Naučte se indexovat a prohledávat částečně strukturované objekty blob služby Azure JSON pomocí Azure Kognitivní hledání rozhraní REST API a post.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: a7a010e3c60d6b96947597878fcd870e9845b2b3
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746133"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Kurz: indexování objektů BLOB JSON z Azure Storage pomocí REST

Azure Kognitivní hledání může indexovat dokumenty JSON a pole ve službě Azure Blob Storage s využitím [indexeru](search-indexer-overview.md) , který ví, jak číst částečně strukturovaná data. Částečně strukturovaná data obsahují značky nebo označení oddělující obsah v rámci dat. Rozdělí rozdíl mezi nestrukturovanými daty, která musí být plně indexována, a formálně strukturovaná data, která jsou v datovém modelu, například ve schématu relační databáze, která lze indexovat podle jednotlivých polí.

V tomto kurzu se používá post a [rozhraní API REST pro vyhledávání](/rest/api/searchservice/) k provádění následujících úloh:

> [!div class="checklist"]
> * Konfigurace zdroje dat služby Azure Kognitivní hledání pro kontejner objektů blob Azure
> * Vytvoření indexu služby Azure Kognitivní hledání, který bude obsahovat prohledávatelný obsah
> * Konfigurace a spuštění indexeru pro čtení kontejneru a extrakce vyhledávaného obsahu z úložiště objektů BLOB v Azure
> * Prohledávání právě vytvořeného indexu

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Desktopová aplikace Postman](https://www.getpostman.com/)
+ [Vytvoření](search-create-service-portal.md) nebo [vyhledání existující vyhledávací služby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Pro tento kurz můžete použít bezplatnou službu. Bezplatná vyhledávací služba omezuje tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Než začnete, ujistěte se, že máte ve své službě místo pro přijímání nových prostředků.

## <a name="download-files"></a>Stažení souborů

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) obsahuje data použitá v tomto kurzu. Stáhnout a rozbalit tento soubor do vlastní složky. Data pocházejí z [ClinicalTrials.gov](https://clinicaltrials.gov/ct2/results), která jsou pro tento kurz převedená na JSON.

## <a name="1---create-services"></a>1. vytvoření služeb

V tomto kurzu se používá Azure Kognitivní hledání k indexování a dotazům a k poskytování dat využívá Azure Blob Storage. 

Pokud je to možné, vytvořte oba ve stejné oblasti a skupině prostředků pro možnost blízkost a spravovatelnost. V praxi může být váš účet Azure Storage v jakékoli oblasti.

### <a name="start-with-azure-storage"></a>Začínáme s Azure Storage

1. [Přihlaste se k Azure Portal](https://portal.azure.com/) a klikněte na **+ vytvořit prostředek**.

1. Vyhledejte *účet úložiště* a vyberte nabídku účtu úložiště od Microsoftu.

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="Vytvořit účet úložiště" border="false":::

1. Na kartě základy jsou vyžadovány následující položky. Přijměte výchozí hodnoty pro všechno ostatní.

   + **Skupina prostředků**. Vyberte existující jednu nebo vytvořte novou, ale použijte stejnou skupinu pro všechny služby, abyste je mohli souhrnně spravovat.

   + **Název účtu úložiště:** Pokud se domníváte, že máte více prostředků stejného typu, použijte název k jednoznačnému odstranění podle typu a oblasti, například *blobstoragewestus*. 

   + **Umístění:** Pokud je to možné, vyberte stejné umístění, které se používá pro Azure Kognitivní hledání a Cognitive Services. Jediné místo má za vyrušení poplatky za šířku pásma.

   + **Druh účtu**. Vyberte výchozí *StorageV2 (obecné účely v2)*.

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** službu vytvořte.

1. Po vytvoření klikněte na **Přejít k prostředku** a otevřete stránku Přehled.

1. Klikněte na služba **BLOB** Service.

1. [Vytvořte kontejner objektů BLOB](../storage/blobs/storage-quickstart-blobs-portal.md) , který bude obsahovat vzorová data. Úroveň veřejného přístupu můžete nastavit na libovolnou z jeho platných hodnot.

1. Po vytvoření kontejneru ho otevřete a na panelu příkazů vyberte **nahrát** .

   :::image type="content" source="media/search-semi-structured-data/upload-command-bar.png" alt-text="Nahrát na panel příkazů" border="false":::

1. Přejděte do složky, která obsahuje ukázkové soubory. Vyberte všechny z nich a pak klikněte na **nahrát**.

   :::image type="content" source="media/search-semi-structured-data/clinicalupload.png" alt-text="Nahrání souborů" border="false":::

Po dokončení nahrávání by se soubory měly zobrazit v samostatné podsložce uvnitř kontejneru dat.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Další prostředek je Azure Kognitivní hledání, který můžete [vytvořit na portálu](search-create-service-portal.md). K dokončení tohoto Názorného postupu můžete použít bezplatnou úroveň. 

Stejně jako u služby Azure Blob Storage si pro získání přístupového klíče chvíli počkejte. Když při zahájení strukturování požadavků začnete, budete muset zadat koncový bod a klíč rozhraní API pro správu, který se použije k ověření každého požadavku.

### <a name="get-a-key-and-url"></a>Získat klíč a adresu URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení**  >  **klíče** Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Získání koncového bodu HTTP a přístupového klíče" border="false":::

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="2---set-up-postman"></a>2 – nastavení post

Spusťte Postman a nastavte požadavek HTTP. Pokud tento nástroj neznáte, přečtěte si téma [vytvoření indexu vyhledávání pomocí rozhraní REST API](search-get-started-rest.md).

Metody žádosti pro každé volání v tomto kurzu jsou **post** a **Get**. K vytvoření zdroje dat, indexu a indexeru provedete tři volání rozhraní API vaší vyhledávací služby. Zdroj dat obsahuje ukazatel na váš účet úložiště a vaše data JSON. Vaše služba Search se připojí při načítání dat.

V části hlavičky nastavte typ Content-Type na `application/json` a nastavte `api-key` na klíč rozhraní API pro správu služby Azure kognitivní hledání. Po nastavení hlaviček je můžete použít pro každý požadavek v tomto cvičení.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="Adresa URL a záhlaví žádosti post" border="false":::

Identifikátory URI musí určovat verzi rozhraní API a každé volání by mělo vrátit **vytvořenou 201**. Obecně dostupná verze API-Version pro použití polí JSON je `2020-06-30` .

## <a name="3---create-a-data-source"></a>3. vytvoření zdroje dat

[Rozhraní API pro vytvoření zdroje dat](/rest/api/searchservice/create-data-source) vytvoří objekt Azure kognitivní hledání, který určuje, jaká data se mají indexovat.

1. Nastavte koncový bod tohoto volání na `https://[service name].search.windows.net/datasources?api-version=2020-06-30` . Nahraďte `[service name]` názvem vaší služby Search. 

1. Zkopírujte následující kód JSON do textu žádosti.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. Nahraďte připojovací řetězec platným řetězcem pro váš účet.

1. Nahraďte [název kontejneru objektů BLOB] kontejnerem, který jste vytvořili pro ukázková data. 

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

## <a name="4---create-an-index"></a>4. vytvoření indexu

Druhé volání je [vytvořit index API](/rest/api/searchservice/create-index), což vytvoří index služby Azure kognitivní hledání, který ukládá všechna hledaná data. Index určuje všechny parametry a jejich atributy.

1. Nastavte koncový bod tohoto volání na `https://[service name].search.windows.net/indexes?api-version=2020-06-30` . Nahraďte `[service name]` názvem vaší služby Search.

1. Zkopírujte následující kód JSON do textu žádosti.

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

## <a name="5---create-and-run-an-indexer"></a>5. vytvoření a spuštění indexeru

Indexer se připojuje ke zdroji dat, importuje data do cílového vyhledávacího indexu a volitelně poskytuje plán pro automatizaci aktualizace dat. REST API je [vytvořit indexer](/rest/api/searchservice/create-indexer).

1. Nastavte identifikátor URI pro toto volání na `https://[service name].search.windows.net/indexers?api-version=2020-06-30` . Nahraďte `[service name]` názvem vaší služby Search.

1. Zkopírujte následující kód JSON do textu žádosti.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. Odešlete požadavek. Požadavek se okamžitě zpracuje. Až se odpověď vrátí zpět, budete mít index, který bude fulltextově prohledávatelné. Odpověď by měla vypadat nějak takto:

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

## <a name="6---search-your-json-files"></a>6. hledání souborů JSON

Hledání můžete zahájit hned po načtení prvního dokumentu.

1. Změňte operaci na **Get**.

1. Nastavte identifikátor URI pro toto volání na `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2020-06-30&$count=true` . Nahraďte `[service name]` názvem vaší služby Search.

1. Odešlete požadavek. Toto je neurčený fulltextový vyhledávací dotaz, který vrací všechna pole, která jsou označena jako načístelné v indexu, spolu s počtem dokumentů. Odpověď by měla vypadat nějak takto:

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

1. Přidejte `$select` parametr dotazu pro omezení výsledků na méně polí: `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2020-06-30&$count=true` .  Pro tento dotaz se 100 dokumentů shodují, ale ve výchozím nastavení Azure Kognitivní hledání vrátí do výsledků pouze 50.

   :::image type="content" source="media/search-semi-structured-data/lastquery.png" alt-text="Parameterizovaný dotaz" border="false":::

1. Příklad složitějšího dotazu by zahrnoval `$filter=MinimumAge ge 30 and MaximumAge lt 75` , který vrací jenom výsledky, pokud je minimální hodnota parametru větší než nebo rovna 30 a maximální hodnota je menší než 75. Nahraďte `$select` výraz `$filter` výrazem.

   :::image type="content" source="media/search-semi-structured-data/metadatashort.png" alt-text="Prohledávání částečně strukturovaných dat" border="false":::

Můžete také použít logické operátory (and, NOT) a operátory porovnání (EQ, ne, gt, lt, GE, Le). Při porovnávání řetězců se rozlišují malá a velká písmena. Další informace a příklady najdete v tématu [Vytvoření jednoduchého dotazu](search-query-simple-examples.md).

> [!NOTE]
> Parametr `$filter` pracuje pouze s metadaty, která se při vytváření indexu označila jako filtrovatelná.

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

Ve fázích předčasného experimentu vývoje je nejužitečnějším přístupem k iteraci návrhu odstranění objektů z Azure Kognitivní hledání a umožnění kódu jejich opětovného sestavení. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Portál můžete použít k odstranění indexů, indexerů a zdrojů dat. Nebo použijte **Delete** a poskytněte adresy URL pro každý objekt. Následující příkaz odstraní indexer.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2020-06-30
```

Při úspěšném odstranění se vrátí kód stavu 204.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete ve vlastním předplatném, je vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu všechny prostředky nebo skupiny prostředků v levém navigačním podokně.

## <a name="next-steps"></a>Další kroky

Teď, když už jste obeznámení se základy indexování objektů BLOB v Azure, se podíváme na konfiguraci indexeru pro objekty blob JSON v Azure Storage.

> [!div class="nextstepaction"]
> [Konfigurace indexování objektů BLOB JSON](search-howto-index-json-blobs.md)