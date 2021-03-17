---
title: Vytvoření úložiště znalostí pomocí REST
titleSuffix: Azure Cognitive Search
description: Pomocí REST API a post se vytvoří Azure Kognitivní hledání Knowledge Store pro zachování rozšíření z kanálu pro rozšíření AI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/18/2020
ms.openlocfilehash: 6af9b8f97d622ae10cfdbcaa8ca50abb42ec7332
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94889047"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Vytvoření úložiště znalostí pomocí REST a předzálohovacího

Znalostní báze obsahuje výstup z kanálu rozšíření Azure Kognitivní hledání pro pozdější analýzu nebo jiné zpracování dat. Kanál obohacený AI akceptuje soubory obrázků nebo nestrukturované textové soubory, indexuje je pomocí Azure Kognitivní hledání, aplikuje rozšíření AI z Cognitive Services (například analýzu obrázků a zpracování přirozeného jazyka) a pak výsledky uloží do úložiště znalostí v Azure Storage. Pomocí nástrojů, jako je Power BI nebo Průzkumník služby Storage, můžete v Azure Portal prozkoumat znalostní bázi Knowledge Store.

V tomto článku použijete rozhraní REST API k ingestování, indexování a používání rozšíření AI na sadu pohlídek za Hotel. Recenze hotelu se importují do úložiště objektů BLOB v Azure. Výsledky se ukládají jako úložiště ve znalostní bázi ve službě Azure Table Storage.

Po vytvoření znalostní báze můžete získat informace o tom, jak získat přístup k znalostnímu obchodu pomocí [Průzkumník služby Storage](knowledge-store-view-storage-explorer.md) nebo [Power BI](knowledge-store-connect-power-bi.md).

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!TIP]
> Pro tento článek doporučujeme [publikovat desktopovou aplikaci](https://www.getpostman.com/) . [Zdrojový kód](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) tohoto článku obsahuje kolekci post obsahující všechny požadavky. 

## <a name="create-services-and-load-data"></a>Vytváření služeb a načítání dat

Tento rychlý Start používá pro AI Azure Kognitivní hledání, Azure Blob Storage a [azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) . 

Vzhledem k tomu, že je zatížení tak malé, Cognitive Services na pozadí po dobu, po kterou se zajišťují bezplatné zpracování až 20 transakcí denně. Vzhledem k tomu, že je datová sada tak malá, můžete přeskočit vytvoření nebo připojení prostředku Cognitive Services.

1. [Stáhněte si HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Tato data jsou data recenze pro hotely uložená v souboru CSV (pocházející z Kaggle.com) a obsahují 19 kusů zpětné vazby od zákazníků k jednomu hotelu. 

1. [Vytvořte si účet Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal) nebo v rámci aktuálního předplatného [Najděte existující účet](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) . Použijete službu Azure Storage pro import nezpracovaného obsahu a úložiště Knowledge v konečném výsledku.

   Vyberte typ účtu **StorageV2 (pro obecné účely v2)** .

1. Otevřete stránky služby BLOB Services a vytvořte kontejner s názvem *hotelové recenze*.

1. Klikněte na **Odeslat**.

    ![Nahrajte data](media/knowledge-store-create-portal/upload-command-bar.png "Nahrajte recenze hotelu.")

1. Vyberte soubor **HotelReviews-Free.csv** , který jste si stáhli v prvním kroku.

    ![Vytvoření kontejneru objektů blob Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Vytvoření kontejneru objektů blob Azure")

1. S tímto prostředkem jste skoro hotovi, ale před tím, než tyto stránky necháte, pomocí odkazu v levém navigačním podokně otevřete stránku **přístupové klíče** . Získání připojovacího řetězce pro načtení dat z úložiště objektů BLOB Připojovací řetězec vypadá podobně jako v následujícím příkladu: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Pořád na portálu přejděte do Azure Kognitivní hledání. [Vytvořte novou službu](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Pro toto cvičení můžete použít bezplatnou službu.

## <a name="configure-postman"></a>Konfigurace nástroje Postman

Nainstalujte a nastavte post.

### <a name="download-and-install-postman"></a>Stáhnout a nainstalovat post

1. Stáhněte [zdrojový kód kolekce po](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Vyberte **File**  >  **importovat** soubor a importujte zdrojový kód do metody post.
1. Vyberte kartu **kolekce** a pak klikněte na tlačítko **...** (tři tečky).
1. Vyberte **Edit** (Upravit). 
   
   ![Pozálohovací aplikace ukazující navigaci](media/knowledge-store-create-rest/postman-edit-menu.png "Přejít do nabídky upravit v poli post")
1. V dialogovém okně **Upravit** vyberte kartu **proměnné** . 

Na kartě **proměnné** můžete přidat hodnoty, které účtují swapy při každém výskytu konkrétní proměnné uvnitř dvojitých složených závorek. Například metoda post nahradí symbol `{{admin-key}}` aktuální hodnotou, pro kterou jste nastavili `admin-key` . Po nahrazení se provede náhrada v adresách URL, hlavičkách, textu žádosti atd. 

Pokud chcete získat hodnotu pro `admin-key` , přejděte do služby Azure kognitivní hledání a vyberte kartu **klíče** . Změňte `search-service-name` `storage-account-name` hodnoty a na hodnoty, které jste zvolili v části [vytvořit služby](#create-services-and-load-data). Nastavte `storage-connection-string` pomocí hodnoty na kartě **přístupové klíče** účtu úložiště. Pro ostatní hodnoty můžete ponechat výchozí hodnoty.

![Karta proměnné aplikace po odeslání](media/knowledge-store-create-rest/postman-variables-window.png "Okno pro proměnné post")


| Proměnná    | Kde je lze získat |
|-------------|-----------------|
| `admin-key` | Na stránce **klíče** služby Azure kognitivní hledání.  |
| `api-version` | Nechejte jako **2020-06-30**. |
| `datasource-name` | Ponechte jako **hotelové recenze – DS**. | 
| `indexer-name` | Ponechte jako **hotelové recenze – IXR**. | 
| `index-name` | Ponechte jako **hotelové recenze – IX**. | 
| `search-service-name` | Název služby Azure Kognitivní hledání. Adresa URL je `https://{{search-service-name}}.search.windows.net` . | 
| `skillset-name` | Ponechte jako **hotelové recenze – SS**. | 
| `storage-account-name` | Název účtu služby Azure Storage. | 
| `storage-connection-string` | V účtu úložiště na kartě **přístupové klíče** vyberte **key1**  >  **připojovací řetězec** klíč1. | 
| `storage-container-name` | Ponechte jako **hotelové recenze**. | 

### <a name="review-the-request-collection-in-postman"></a>Kontrola kolekce požadavků v poli pro odeslání

Při vytváření znalostní báze musíte vydat čtyři požadavky HTTP: 

- **Vložit požadavek na vytvoření indexu**: Tento index obsahuje data, která Azure kognitivní hledání používá a vrací.
- **Post – požadavek na vytvoření zdroje dat**: Tento zdroj dat připojuje vaše chování Azure kognitivní hledání k účtu úložiště dat a úložiště znalostní báze. 
- **Put – požadavek na vytvoření dovednosti**: dovednosti určuje rozšíření, která se aplikují na vaše data a strukturu znalostní databáze.
- **Vložit požadavek na vytvoření indexeru**: když spustíte indexer, načte data, použije dovednosti a výsledky uloží. Tuto žádost musíte spustit jako poslední.

[Zdrojový kód](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) obsahuje kolekci post, která obsahuje čtyři požadavky. Chcete-li vydat žádosti, vyberte v poli post kartu žádosti. Pak přidejte `api-key` záhlaví a `Content-Type` žádosti. Nastavte hodnotu `api-key` na `{{admin-key}}` . Nastavte hodnotu `Content-type` na `application/json` . 

![Snímek obrazovky znázorňující rozhraní post 's Headers](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Musíte nastavit `api-key` a `Content-type` hlavičky všech svých požadavků. Pokud post rozpoznává proměnnou, proměnná se zobrazí v oranžovém textu, stejně jako `{{admin-key}}` v předchozím snímku obrazovky. Pokud je proměnná nesprávně napsaná, zobrazí se červený text.
>

## <a name="create-an-azure-cognitive-search-index"></a>Vytvoření indexu Azure Cognitive Search

Vytvořte index služby Azure Kognitivní hledání, který bude reprezentovat data, která vás zajímají při hledání, filtrování a používání vylepšení. Vytvořte index vydáním žádosti o vložení do `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` . Metoda post nahradí symboly, které jsou uzavřeny ve složených závorkách (například `{{search-service-name}}` , `{{index-name}}` a `{{api-version}}` ), hodnotami, které jste nastavili v [konfiguraci post](#configure-postman). Pokud používáte jiný nástroj k vystavení příkazů REST, je nutné tyto proměnné nahradit sami.

V těle žádosti nastavte strukturu indexu služby Azure Kognitivní hledání. V části potom po nastavení `api-key` `Content-type` záhlaví a přejít do podokna **tělo** žádosti. Měl by se zobrazit následující kód JSON. Pokud to neuděláte, vyberte **nezpracovaný formát**  >  **JSON (Application/JSON)** a vložte následující kód jako text:

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

Tato definice indexu je kombinací dat, která se mají prezentovat uživateli (název hotelu, revize obsahu, datum), metadata hledání a data o vylepšení AI (mínění, klíčová fráze a jazyk).

Vyberte **Odeslat** a vydejte požadavek PUT. Měl by se zobrazit stav `201 - Created` . Pokud se zobrazí jiný stav, vyhledejte v podokně **tělo** odpověď JSON, která obsahuje chybovou zprávu. 

## <a name="create-the-datasource"></a>Vytvoření zdroje dat

Dále připojte službu Azure Kognitivní hledání k datům hotelu uloženým v úložišti objektů BLOB. Chcete-li vytvořit zdroj dat, odešlete požadavek POST na `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` . Je nutné nastavit `api-key` záhlaví a, `Content-Type` jak je popsáno výše. 

V poli pro odeslání klikněte na požadavek **vytvořit zdroj dat** a pak na podokno **tělo** . Měl by se zobrazit následující kód:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Vyberte **Odeslat** pro vydání žádosti post. 

## <a name="create-the-skillset"></a>Vytvoření dovednosti 

Dalším krokem je zadání dovednosti, které určuje jak vylepšení použít, tak úložiště znalostí, kde se výsledky uloží. V poli pro odeslání vyberte kartu **vytvořit dovednosti** . Tento požadavek odešle do `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}` . Nastavte `api-key` hlavičky a `Content-type` jako dříve. 

Existují dva velké objekty nejvyšší úrovně: `skills` a `knowledgeStore` . Každý objekt uvnitř `skills` objektu je služba rozšíření. Každá služba pro rozšíření má `inputs` a `outputs` . `LanguageDetectionSkill`Obsahuje výstup `targetName` `Language` . Hodnota tohoto uzlu je používána většinou dalších dovedností jako vstup. Zdroj je `document/Language` . Schopnost používat výstup jednoho uzlu jako vstup na jiný je ještě více zjevné v `ShaperSkill` , který určuje, jak se data budou natékat do tabulek ve znalostní bázi Knowledge Store.

`knowledge_store`Objekt se připojí k účtu úložiště prostřednictvím `{{storage-connection-string}}` proměnné post. `knowledge_store` obsahuje sadu mapování mezi vylepšeným dokumentem a tabulkami a sloupci ve znalostní bázi Knowledge Store. 

Pokud chcete vygenerovat dovednosti, vyberte tlačítko Odeslat v poli pro **odeslání** žádosti:

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

Posledním krokem je vytvoření indexeru. Indexer načte data a aktivuje dovednosti. V části indexer vyberte požadavek **vytvořit indexer** a pak zkontrolujte text. Definice indexeru odkazuje na několik dalších prostředků, které jste už vytvořili: zdroj dat, index a dovednosti. 

`parameters/configuration`Objekt určuje, jak indexer ingestuje data. V takovém případě se vstupní data nachází v jednom dokumentu, který obsahuje řádek záhlaví a hodnoty oddělené čárkami. Klíč dokumentu je jedinečný identifikátor pro dokument. Před kódováním je klíč dokumentu adresou URL zdrojového dokumentu. Nakonec jsou výstupní hodnoty dovednosti, jako je kód jazyka, mínění a klíčové fráze, namapovány na jejich umístění v dokumentu. I když existuje jedna hodnota pro `Language` , `Sentiment` je použita na každý prvek v poli `pages` . `Keyphrases` je pole, které je také použito pro každý prvek v `pages` poli.

Po nastavení `api-key` `Content-type` hlaviček a potvrďte, že text požadavku je podobný následujícímu zdrojovému kódu, vyberte **Odeslat** v poli post. Post odešle požadavek PUT na `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}` . Azure Kognitivní hledání vytvoří a spustí indexer. 

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

V Azure Portal přejdete na stránku **Přehled** služby Azure kognitivní hledání. Vyberte kartu **indexery** a pak vyberte **hotely-recenze – IXR**. Pokud indexer ještě není spuštěný, vyberte **Spustit**. Úloha indexování může vyvolávat některá upozornění související s rozpoznáváním jazyka. Data obsahují některé recenze, které jsou napsané v jazycích, které ještě nejsou podporované znalostmi rozpoznávání. 

## <a name="next-steps"></a>Další kroky

Teď, když jste rozrostli data pomocí Cognitive Services a prozkoumali výsledky do obchodu Knowledge Store, můžete k prozkoumání obohacené sady dat použít Průzkumník služby Storage nebo Power BI.

Další informace o tom, jak prozkoumat toto úložiště znalostí pomocí Průzkumník služby Storage, najdete v tomto návodu:

> [!div class="nextstepaction"]
> [Zobrazení s využitím Průzkumníka služby Storage](knowledge-store-view-storage-explorer.md)

Další informace o tom, jak se připojit k tomuto znalostnímu obchodu pro Power BI, najdete v tomto návodu:

> [!div class="nextstepaction"]
> [Propojení s Power BI](knowledge-store-connect-power-bi.md)

Pokud chcete tento cvičení opakovat nebo si vyzkoušet jiný návod k rozšíření AI, odstraňte indexer **idxr hotelového přezkoumání** . Odstranění indexeru obnoví čítač bezplatných denních transakcí na nulu.