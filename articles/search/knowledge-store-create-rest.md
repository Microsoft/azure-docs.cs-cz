---
title: Vytvoření úložiště znalostí pomocí Azure Search REST
description: Pomocí REST API a potom můžete vytvořit úložiště Azure Search znalostní báze pro zachovávání rozšíření z kanálu vyhledávání vnímání.
author: lobrien
services: search
ms.service: search
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: laobri
ms.openlocfilehash: b67f0cf60d279c7bc52b4114d29c37847f5c57f1
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244467"
---
# <a name="create-an-azure-search-knowledge-store-by-using-rest"></a>Vytvoření Azure Search Knowledge Store pomocí REST

Funkce znalostní báze ve službě Azure Search uchovává výstup kanálu rozšíření AI pro pozdější analýzu nebo jiné zpracování pro příjem dat. Kanál obohacený AI akceptuje soubory obrázků nebo nestrukturované textové soubory, indexuje je pomocí Azure Search, aplikuje rozšíření AI z Azure Cognitive Services (jako je analýza obrázků a zpracování přirozeného jazyka), a pak výsledky uloží do znalostní báze. Uložit v Azure Storage. Pomocí nástrojů, jako je Power BI nebo Průzkumník služby Storage, můžete v Azure Portal prozkoumat znalostní bázi Knowledge Store.

V tomto článku použijete rozhraní REST API k ingestování, indexování a používání rozšíření AI na sadu pohlídek za Hotel. Recenze hotelu se importují do úložiště objektů BLOB v Azure. Výsledky se ukládají jako úložiště ve znalostní bázi ve službě Azure Table Storage.

Po vytvoření znalostní báze můžete získat informace o tom, jak získat přístup k znalostnímu obchodu pomocí [Průzkumník služby Storage](knowledge-store-view-storage-explorer.md) nebo [Power BI](knowledge-store-connect-power-bi.md).

## <a name="create-services"></a>Vytvořit služby

Vytvořte následující služby:

- Vytvořte [službu Azure Search](search-create-service-portal.md) nebo [vyhledejte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v aktuálním předplatném. Pro tento kurz můžete použít bezplatnou službu.

- Vytvořte [účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pro ukládání ukázkových dat a úložiště znalostí. Váš účet úložiště musí pro službu Azure Search používat stejné umístění (například USA – západ). Hodnota pro **druh účtu** musí být **StorageV2 (pro obecné účely v2)** (výchozí) nebo **Storage (pro obecné účely V1)** .

- Doporučené: Získejte [aplikaci po ploše](https://www.getpostman.com/) pro odesílání požadavků do Azure Search. REST API můžete použít s jakýmkoli nástrojem, který dokáže pracovat s požadavky a odpověďmi HTTP. Jako dodatečná volba pro zkoumání rozhraní REST API je vhodná. V tomto článku používáme post. [Zdrojový kód](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) pro tento článek také obsahuje kolekci požadavků post. 

## <a name="store-the-data"></a>Uložení dat

Načtěte soubor. CSV pro kontrolu hotelu do úložiště objektů BLOB v Azure, aby k němu měl k dispozici Azure Search indexer a mohl by být vydáván prostřednictvím kanálu pro rozšíření AI.

### <a name="create-a-blob-container-by-using-the-data"></a>Vytvoření kontejneru objektů BLOB pomocí dat

1. Stáhněte [si data kontroly hotelu](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) uložená v souboru CSV (HotelReviews_Free. csv). Tato data pocházejí z Kaggle.com a obsahují názory zákazníků na hotely.
1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejít na účet služby Azure Storage.
1. Vytvořte [kontejner objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Pokud chcete vytvořit kontejner, v levé nabídce účtu úložiště vyberte **objekty blob**a pak vyberte **kontejner**.
1. Pro nový **název**kontejneru zadejte **hotelové recenze**.
1. Jako **úroveň veřejného přístupu**vyberte libovolnou hodnotu. Použili jsme výchozí.
1. Vyberte **OK** a vytvořte kontejner objektů BLOB.
1. Otevřete nový **seznam hotelů – kontrola** kontejneru, vyberte **nahrát**a potom vyberte soubor HotelReviews-Free. csv, který jste stáhli v prvním kroku.

    ![Nahrání dat]nahrání(media/knowledge-store-create-portal/upload-command-bar.png "pro posuzujecí Hotel")

1. Vyberte **nahrát** a IMPORTUJTE soubor CSV do úložiště objektů BLOB v Azure. Zobrazí se nový kontejner:

    ![Vytvoření kontejneru objektů BLOB](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "vytvoření kontejneru objektů BLOB")

## <a name="configure-postman"></a>Konfigurovat metodu post

Nainstalujte a nastavte post.

### <a name="download-and-install-postman"></a>Stáhnout a nainstalovat post

1. Stáhněte [zdrojový kód kolekce po](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Vyberte **soubor** > **Import** pro import zdrojového kódu do metody post.
1. Vyberte kartu **kolekce** a pak klikněte na tlačítko **...** (tři tečky).
1. Vyberte **Upravit**. 
   
   ![Pozálohovací aplikace ukazující navigaci](media/knowledge-store-create-rest/postman-edit-menu.png "Přejít do nabídky upravit v příspěvku")
1. V dialogovém okně **Upravit** vyberte kartu **proměnné** . 

Na kartě **proměnné** můžete přidat hodnoty, které účtují swapy při každém výskytu konkrétní proměnné uvnitř dvojitých složených závorek. Například metoda post nahradí symbol `{{admin-key}}` aktuální hodnotou, kterou jste nastavili pro `admin-key`. Po nahrazení se provede náhrada v adresách URL, hlavičkách, textu žádosti atd. 

Pokud chcete získat hodnotu pro `admin-key`, přejděte do služby Azure Search a vyberte kartu **klíče** . Změňte `search-service-name` a `storage-account-name` na hodnoty, které jste zvolili v části [vytvořit služby](#create-services). Nastavte `storage-connection-string` pomocí hodnoty na kartě **přístupové klíče** účtu úložiště. Pro ostatní hodnoty můžete ponechat výchozí hodnoty.

(media/knowledge-store-create-rest/postman-variables-window.png "Okno proměnné pro") vystavení ![záložky pro vystavení aplikace]


| Proměnná    | Kde ho získat |
|-------------|-----------------|
| `admin-key` | Na kartě **klíče** služby Azure Search.  |
| `api-version` | Ponechte jako **2019-05-06-Preview**. |
| `datasource-name` | Ponechte jako **hotelové recenze – DS**. | 
| `indexer-name` | Ponechte jako **hotelové recenze – IXR**. | 
| `index-name` | Ponechte jako **hotelové recenze – IX**. | 
| `search-service-name` | Hlavní název služby Azure Search. Adresa URL je `https://{{search-service-name}}.search.windows.net`. | 
| `skillset-name` | Ponechte jako **hotelové recenze – SS**. | 
| `storage-account-name` | Hlavní název účtu úložiště | 
| `storage-connection-string` | V účtu úložiště na kartě **přístupové klíče** vyberte **klíč1** **připojovací řetězec** > . | 
| `storage-container-name` | Ponechte jako **hotelové recenze**. | 

### <a name="review-the-request-collection-in-postman"></a>Kontrola kolekce požadavků v poli pro odeslání

Při vytváření znalostní báze musíte vydat čtyři požadavky HTTP: 

- **Vložit požadavek na vytvoření indexu**: Tento index obsahuje data, která Azure Search používá a vrací.
- **Post – požadavek na vytvoření zdroje dat**: Tento zdroj dat připojuje vaše chování Azure Search k účtu úložiště dat a úložiště znalostní báze. 
- **Put – požadavek na vytvoření dovednosti**: dovednosti určuje rozšíření, která se aplikují na vaše data a strukturu znalostní databáze.
- **Vložit požadavek na vytvoření indexeru**: když spustíte indexer, načte data, použije dovednosti a výsledky uloží. Tuto žádost musíte spustit jako poslední.

[Zdrojový kód](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) obsahuje kolekci post, která obsahuje čtyři požadavky. Chcete-li vydat žádosti, vyberte v poli post kartu žádosti. Pak přidejte hlavičky žádostí `api-key` a `Content-Type`. Nastavte hodnotu `api-key` na `{{admin-key}}`. Nastavte hodnotu `Content-type` na `application/json`. 

![Snímek obrazovky znázorňující rozhraní post 's Headers](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Ve všech svých žádostech musíte nastavit `api-key` a `Content-type` hlaviček. Pokud post rozpoznává proměnnou, proměnná se zobrazí v oranžovém textu, stejně jako u `{{admin-key}}` na předchozím snímku obrazovky. Pokud je proměnná nesprávně napsaná, zobrazí se červený text.
>

## <a name="create-an-azure-search-index"></a>Vytvoření indexu Azure Search

Vytvořte index Azure Search, který bude reprezentovat data, která vás zajímají při hledání, filtrování a používání vylepšení. Vytvořte index tím, že vydáte požadavek PUT `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. Metoda post nahradí symboly, které jsou uzavřeny ve složených závorkách (například `{{search-service-name}}`, `{{index-name}}` a `{{api-version}}`) hodnotami, které jste nastavili v části [Konfigurace post](#configure-postman). Pokud používáte jiný nástroj k vystavení příkazů REST, je nutné tyto proměnné nahradit sami.

Nastavte strukturu Azure Search indexu v těle žádosti. V poli po nastavení hlaviček `api-key` a `Content-type` přejdete do podokna **tělo** žádosti. Měl by se zobrazit následující kód JSON. Pokud to neuděláte, vyberte **Nezpracovaná**@no__t**JSON (Application/JSON)** a vložte následující kód jako text:

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

Vyberte **Odeslat** a vydejte požadavek PUT. Měl by se zobrazit stav `201 - Created`. Pokud se zobrazí jiný stav, vyhledejte v podokně **tělo** odpověď JSON, která obsahuje chybovou zprávu. 

## <a name="create-the-datasource"></a>Vytvoření zdroje dat

Dále připojte Azure Search k datům hotelu, která jste uložili v [úložišti dat](#store-the-data). Chcete-li vytvořit zdroj dat, odešlete požadavek POST na `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. Je nutné nastavit hlavičky `api-key` a `Content-Type`, jak je popsáno výše. 

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

Dalším krokem je zadání dovednosti, které určuje jak vylepšení použít, tak úložiště znalostí, kde se výsledky uloží. V poli pro odeslání vyberte kartu **vytvořit dovednosti** . Tento požadavek pošle `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`. Nastavte hlavičky `api-key` a `Content-type` jako dříve. 

Existují dva velké objekty nejvyšší úrovně: `skills` a `knowledgeStore`. Každý objekt uvnitř objektu `skills` je služba rozšíření. Každá služba pro rozšíření má `inputs` a `outputs`. @No__t-0 má výstupní `targetName` `Language`. Hodnota tohoto uzlu je používána většinou dalších dovedností jako vstup. Zdroj je `document/Language`. Schopnost používat výstup jednoho uzlu jako vstup do druhého je ještě více zjevné v `ShaperSkill`, což určuje, jak se data budou natékat do tabulek ve znalostní bázi Knowledge Store.

Objekt `knowledge_store` se připojí k účtu úložiště přes proměnnou post `{{storage-connection-string}}`. `knowledge_store` obsahuje sadu mapování mezi vylepšeným dokumentem a tabulkami a sloupci ve znalostní bázi Knowledge Store. 

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

Objekt `parameters/configuration` řídí, jak indexer ingestuje data. V takovém případě se vstupní data nachází v jednom dokumentu, který obsahuje řádek záhlaví a hodnoty oddělené čárkami. Klíč dokumentu je jedinečný identifikátor pro dokument. Před kódováním je klíč dokumentu adresou URL zdrojového dokumentu. Nakonec jsou výstupní hodnoty dovednosti, jako je kód jazyka, mínění a klíčové fráze, namapovány na jejich umístění v dokumentu. I když existuje jedna hodnota pro `Language`, `Sentiment` se aplikuje na každý prvek v poli `pages`. `Keyphrases` je pole, které je také použito pro každý prvek v poli `pages`.

Po nastavení hlaviček `api-key` a `Content-type` a potvrzení, že text žádosti je podobný následujícímu zdrojovému kódu, vyberte **Odeslat** v poli post. Příkaz POST odešle požadavek PUT na `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. Azure Search vytvoří a spustí indexer. 

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

## <a name="run-the-indexer"></a>Spustit indexer 

V Azure Portal přejdete na stránku **Přehled** služby Azure Search. Vyberte kartu **indexery** a pak vyberte **hotely-recenze – IXR**. Pokud indexer ještě není spuštěný, vyberte **Spustit**. Úloha indexování může vyvolávat některá upozornění související s rozpoznáváním jazyka. Data obsahují některé recenze, které jsou napsané v jazycích, které ještě nejsou podporované znalostmi rozpoznávání. 

## <a name="next-steps"></a>Další kroky

Teď, když jste rozrostli data pomocí Cognitive Services a prozkoumali výsledky do obchodu Knowledge Store, můžete k prozkoumání obohacené sady dat použít Průzkumník služby Storage nebo Power BI.

Další informace o tom, jak prozkoumat toto úložiště znalostí pomocí Průzkumník služby Storage, najdete v tomto návodu:

> [!div class="nextstepaction"]
> [Zobrazit s Průzkumník služby Storage](knowledge-store-view-storage-explorer.md)

Další informace o tom, jak se připojit k tomuto znalostnímu obchodu pro Power BI, najdete v tomto návodu:

> [!div class="nextstepaction"]
> [Připojení pomocí Power BI](knowledge-store-connect-power-bi.md)

Pokud chcete tento cvičení opakovat nebo si vyzkoušet jiný návod k rozšíření AI, odstraňte indexer **idxr hotelového přezkoumání** . Odstranění indexeru obnoví čítač bezplatných denních transakcí na nulu.
