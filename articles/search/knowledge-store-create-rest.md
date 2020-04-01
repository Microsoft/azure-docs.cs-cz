---
title: Vytvoření úložiště znalostí (náhledu) pomocí rest
titleSuffix: Azure Cognitive Search
description: Pomocí rozhraní REST API a Postman vytvořit úložiště znalostní báze Azure Cognitive Search pro trvalé obohacení z kanálu obohacení AI. Tato funkce je aktuálně ve verzi Public Preview.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 12/30/2019
ms.openlocfilehash: 478a7e03b432006b429c96e03307fd8e494c88ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472311"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Vytvoření úložiště znalostí pomocí REST a Postman

> [!IMPORTANT] 
> Úložiště znalostí je v současné době ve verzi Public Preview. Funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje funkce náhledu. V současné době je omezená podpora portálu a žádná podpora sady .NET SDK.

Úložiště znalostí obsahuje výstup z kanálu obohacení Azure Cognitive Search pro pozdější analýzu nebo jiné následné zpracování. Kanál obohacený umělou ai přijímá obrazové soubory nebo nestrukturované textové soubory, indexuje je pomocí Azure Cognitive Search, použije obohacení ai ze služeb Cognitive Services (jako je analýza image a zpracování přirozeného jazyka) a pak uloží výsledky do úložiště znalostí ve službě Azure Storage. Pomocí nástrojů, jako je Power BI nebo Storage Explorer na webu Azure Portal, můžete prozkoumat úložiště znalostí.

V tomto článku použijete rozhraní rozhraní ROZHRANÍ REST API k ingestování, indexování a použití obohacení ai na sadu recenzí hotelů. Recenze hotelů se importují do úložiště objektů Blob Azure. Výsledky se ukládají jako úložiště znalostí v úložišti Azure Table.

Po vytvoření úložiště znalostí se dozvíte, jak získat přístup k úložišti znalostí pomocí [Průzkumníka úložiště](knowledge-store-view-storage-explorer.md) nebo [Power BI](knowledge-store-connect-power-bi.md).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

> [!TIP]
> Pro tento článek doporučujeme [desktopovou aplikaci Postman.](https://www.getpostman.com/) [Zdrojový kód](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) pro tento článek obsahuje Pošťák kolekce obsahující všechny požadavky. 

## <a name="create-services-and-load-data"></a>Vytváření služeb a načítání dat

Tento rychlý start používá Azure Cognitive Search, Azure Blob storage a [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) pro UI. 

Vzhledem k tomu, že zatížení je tak malé, služby Cognitive Services jsou poklepány na pozadí, aby poskytovaly bezplatné zpracování až pro 20 transakcí denně. Vzhledem k tomu, že sada dat je tak malá, můžete přeskočit vytváření nebo připojení prostředku služeb Cognitive Services.

1. [Stáhnout HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Tato data jsou údaje o kontrole hotelu uložené v souboru CSV (pocházející z Kaggle.com) a obsahují 19 částí zpětné vazby od zákazníků o jednom hotelu. 

1. [Vytvořte si účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) nebo [najděte existující účet](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) v rámci aktuálního předplatného. Budete používat úložiště Azure pro nezpracovaný obsah, který se má importovat, a úložiště znalostí, které je konečným výsledkem.

   Zvolte typ účtu **StorageV2 (pro obecné účely V2).**

1. Otevřete stránky služby Blob a vytvořte kontejner s názvem *recenze hotelů*.

1. Klikněte na **Odeslat**.

    ![Nahrání dat](media/knowledge-store-create-portal/upload-command-bar.png "Nahrajte recenze hotelů")

1. Vyberte soubor **HotelReviews-Free.csv,** který jste stáhli v prvním kroku.

    ![Vytvoření kontejneru objektů blob Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Vytvoření kontejneru objektů blob Azure")

1. S tímto zdrojem jste téměř hotovi, ale před opuštěním těchto stránek otevřete stránku **Přístupové klávesy** pomocí odkazu v levém navigačním podokně. Získejte připojovací řetězec pro načtení dat z úložiště objektů Blob. Připojovací řetězec vypadá podobně jako v následujícím příkladu:`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Stále na portálu, přepněte na Azure Cognitive Search. [Vytvořte novou službu](search-create-service-portal.md) nebo [vyhledejte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Pro toto cvičení můžete využít bezplatnou službu.

## <a name="configure-postman"></a>Konfigurace nástroje Postman

Nainstalujte a nastavte Pošťáka.

### <a name="download-and-install-postman"></a>Stáhnout a nainstalovat Postman

1. Stáhněte si [zdrojový kód kolekce Postman](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Vyberte **Import souborů,** > **Import** chcete-li importovat zdrojový kód do Posuzovacího muže.
1. Vyberte kartu **Kolekce** a pak vyberte tlačítko **...** (tři tečky).
1. Vyberte **Upravit**. 
   
   ![Pošťák aplikace zobrazující navigaci](media/knowledge-store-create-rest/postman-edit-menu.png "Přejít do nabídky Upravit v Pošťákovi")
1. V dialogovém okně **Upravit** vyberte kartu **Proměnné.** 

Na kartě **Proměnné** můžete přidat hodnoty, které Postman zamění pokaždé, když narazí na určitou proměnnou uvnitř dvojité závorky. Například Postman nahradí symbol `{{admin-key}}` aktuální hodnotou, kterou `admin-key`jste nastavili pro . Pošťák provede nahrazení v adresách URL, záhlaví, tělo požadavku a tak dále. 

Chcete-li získat `admin-key`hodnotu pro , přejděte na službu `search-service-name` `storage-account-name` Azure Cognitive Search a vyberte kartu **Klíče.** Změna a hodnoty, které jste zvolili v [příkazu Vytvořit služby](#create-services-and-load-data). Nastavení `storage-connection-string` pomocí hodnoty na kartě **Přístupové klíče** účtu úložiště. Výchozí hodnoty můžete ponechat pro ostatní hodnoty.

![Karta Proměnné aplikace Pošťák](media/knowledge-store-create-rest/postman-variables-window.png "Okno proměnných pošťáka")


| Proměnná    | Kde je lze získat |
|-------------|-----------------|
| `admin-key` | Na stránce **Klíče** služby Azure Cognitive Search.  |
| `api-version` | Nechte jako **2019-05-06-Preview**. |
| `datasource-name` | Odejděte jako **hotel-reviews-ds**. | 
| `indexer-name` | Odejděte jako **hotel-reviews-ixr**. | 
| `index-name` | Odejděte jako **hotel-reviews-ix**. | 
| `search-service-name` | Název služby Azure Cognitive Search. Adresa URL `https://{{search-service-name}}.search.windows.net`je . | 
| `skillset-name` | Odejděte jako **hotel-recenze-ss**. | 
| `storage-account-name` | Název účtu úložiště. | 
| `storage-connection-string` | V účtu úložiště vyberte na kartě **Přístupové klíče** **připojovací řetězec** **key1** > . | 
| `storage-container-name` | Odejděte jako **recenze hotelů**. | 

### <a name="review-the-request-collection-in-postman"></a>Kontrola kolekce žádostí v Postman

Při vytváření úložiště znalostí je nutné vydat čtyři požadavky HTTP: 

- **POŽADAVEK PUT k vytvoření indexu**: Tento index obsahuje data, která azure cognitive search používá a vrací.
- **POST request to create the datasource**: This datasource connects your Azure Cognitive Search behavior to the data and knowledge store's storage account. 
- **POŽADAVEK PUT k vytvoření sady dovedností**: Skillset určuje obohacení, které jsou použity pro vaše data a strukturu úložiště znalostí.
- **PUT požadavek na vytvoření indexeru**: Spuštění indexeru čte data, použije skillset a uloží výsledky. Tento požadavek je nutné spustit jako poslední.

[Zdrojový kód](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) obsahuje pořštivu kolekce, která má čtyři požadavky. Chcete-li vydat požadavky, vyberte v pořšti v položce kartu pro požadavek. Potom `api-key` přidejte `Content-Type` a požádejte záhlaví. Nastavte hodnotu `api-key` `{{admin-key}}`na . Nastavte `Content-type` hodnotu `application/json`na . 

![Snímek obrazovky s postmanovým rozhraním pro záhlaví](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Je nutné `api-key` `Content-type` nastavit a záhlaví ve všech vašich požadavků. Pokud Poskakovatec rozpoznává proměnnou, `{{admin-key}}` proměnná se zobrazí v oranžovém textu, stejně jako na předchozím snímku obrazovky. Pokud je proměnná chybně napsaná, zobrazí se červeně.
>

## <a name="create-an-azure-cognitive-search-index"></a>Vytvoření indexu Azure Cognitive Search

Vytvořte index Azure Cognitive Search, který bude představovat data, která vás zajímají o vyhledávání, filtrování a použití vylepšení. Vytvořte index vydáním požadavku `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`PUT společnosti . Pošťák nahradí symboly, které jsou uzavřeny `{{search-service-name}}` `{{index-name}}`ve `{{api-version}}`dvouzávlech (například , a ) s hodnotami, které jste nastavili v [Konfigurovat postman](#configure-postman). Pokud k vydávání příkazů REST používáte jiný nástroj, musíte tyto proměnné nahradit sami.

Nastavte strukturu indexu Azure Cognitive Search v těle požadavku. V Pošťák, po `api-key` `Content-type` nastavení záhlaví a, přejděte do panelu **Body** požadavku. Měli byste vidět následující JSON. Pokud tak nechcete, vyberte **položku Raw** > **JSON (aplikace/json)** a pak jako text vložte následující kód:

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

Tato definice indexu je kombinací dat, která chcete uživateli prezentovat (název hotelu, obsah recenze, datum), metadata vyhledávání a data vylepšení ai (mínění, klíčové fráze a jazyk).

Chcete-li vydat požadavek PUT, vyberte **možnost Odeslat.** Měli byste vidět `201 - Created`stav . Pokud se zobrazí jiný stav, vyhledejte v podokně **Body** odpověď JSON, která obsahuje chybovou zprávu. 

## <a name="create-the-datasource"></a>Vytvoření zdroje dat

Dále připojte Azure Cognitive Search k hotelovým datům uloženým v úložišti objektů Blob. Chcete-li vytvořit zdroj dat, `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`odešlete požadavek POST společnosti . Je nutné `api-key` nastavit `Content-Type` záhlaví a, jak bylo popsáno dříve. 

V Postman, přejděte na vytvořit zdroj **dat** požadavek a potom na **body** podokno. Měl by se zobrazit následující kód:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Chcete-li vydat požadavek POST, vyberte **možnost Odeslat.** 

## <a name="create-the-skillset"></a>Vytvoření sady dovedností 

Dalším krokem je určení sady dovedností, která určuje jak vylepšení, která mají být použita, tak úložiště znalostí, kde budou výsledky uloženy. V pošťáku vyberte kartu **Vytvořit sadu dovedností.** Tento požadavek odešle PUT do `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`. Nastavte `api-key` záhlaví `Content-type` a stejně jako dříve. 

Existují dva velké objekty `skills` nejvyšší `knowledgeStore`úrovně: a . Každý objekt `skills` uvnitř objektu je služba obohacení. Každá služba obohacování má `inputs` a `outputs`. Má `LanguageDetectionSkill` výstup `targetName` `Language`. Hodnota tohoto uzlu je používán většina ostatních dovedností jako vstup. Zdroj je `document/Language`. Schopnost použití výstupu jednoho uzlu jako vstupu do jiného je `ShaperSkill`ještě patrnější v , která určuje, jak data proudí do tabulek úložiště znalostí.

Objekt `knowledge_store` se připojí k účtu `{{storage-connection-string}}` úložiště prostřednictvím postman proměnné. `knowledge_store`obsahuje sadu mapování mezi rozšířeným dokumentem a tabulkami a sloupci v úložišti znalostí. 

Chcete-li generovat sadu dovedností, vyberte tlačítko **Odeslat** v Pošťákovi pro odeslání požadavku:

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>Vytvoření indexeru

Posledním krokem je vytvoření indexeru. Indexer přečte data a aktivuje skillset. V pošťáku vyberte požadavek **Vytvořit indexer** a potom zkontrolujte tělo. Definice indexeru odkazuje na několik dalších prostředků, které jste již vytvořili: zdroj dat, index a skillset. 

Objekt `parameters/configuration` určuje, jak indexer ingestuje data. V tomto případě jsou vstupní data v jednom dokumentu, který má řádek záhlaví a hodnoty oddělené čárkami. Klíč dokumentu je jedinečný identifikátor dokumentu. Před kódováním je klíč dokumentu adresou URL zdrojového dokumentu. Nakonec výstupní hodnoty sady dovedností, jako je kód jazyka, mínění a klíčové fráze, jsou mapovány na jejich umístění v dokumentu. I když je jedna `Language`hodnota `Sentiment` pro , se použije `pages`na každý prvek v poli . `Keyphrases`je pole, které je také použito `pages` pro každý prvek v poli.

Po nastavení `api-key` záhlaví `Content-type` a a potvrďte, že tělo požadavku je podobné následujícímu zdrojovému kódu, vyberte **Odeslat** v Postman. Pošťák odešle `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`žádost PUT společnosti . Azure Cognitive Search vytvoří a spustí indexer. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>Spuštění indexeru 

Na webu Azure Portal přejděte na stránku **Přehled** služby Azure Cognitive Search. Vyberte kartu **Indexery** a pak vyberte **hotels-reviews-ixr**. Pokud indexer ještě neběžel, vyberte **Spustit**. Úloha indexování může vyvolat některá upozornění související s rozpoznáváním jazyka. Data zahrnují některé recenze, které jsou napsány v jazycích, které ještě nejsou podporovány kognitivními dovednostmi. 

## <a name="next-steps"></a>Další kroky

Teď, když jste svá data obohatili pomocí služeb Cognitive Services a promítli výsledky do úložiště znalostí, můžete pomocí Průzkumníka úložiště nebo Power BI prozkoumat vaši obohacenou datovou sadu.

Informace o tom, jak prozkoumat toto úložiště znalostí pomocí Průzkumníka úložiště, najdete v tomto návodu:

> [!div class="nextstepaction"]
> [Zobrazení s využitím Průzkumníka služby Storage](knowledge-store-view-storage-explorer.md)

Informace o připojení tohoto úložiště znalostí k Power BI najdete v tomto návodu:

> [!div class="nextstepaction"]
> [Propojení s Power BI](knowledge-store-connect-power-bi.md)

Pokud chcete toto cvičení zopakovat nebo vyzkoušet jiný návod k obohacení umělou ai, odstraňte indexer **hotel-reviews-idxr.** Odstraněním indexeru obnovíte počítadlo transakcí volného denního na nulu.
