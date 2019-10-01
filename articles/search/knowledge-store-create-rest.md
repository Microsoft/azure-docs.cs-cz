---
title: Vytvoření úložiště znalostí pomocí Azure Search REST
description: Pomocí REST API a post můžete vytvořit Azure Search znalostní báze pro zachování rozšíření z kanálu vyhledávání rozpoznávání.
author: lobrien
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: laobri
ms.openlocfilehash: ae0694c4c79527ef3b64ad68d32ef3bce0150462
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703574"
---
# <a name="create-an-azure-search-knowledge-store-using-rest"></a>Vytvoření Azure Search Knowledge Store pomocí REST

Znalostní báze je funkce Azure Search, která uchovává výstup kanálu rozšíření AI pro pozdější analýzu nebo jiné zpracování dat. Kanál obohacený AI akceptuje soubory obrázků nebo nestrukturované textové soubory, indexuje je pomocí Azure Search, aplikuje rozšíření AI z Cognitive Services (například analýzu obrázků a zpracování přirozeného jazyka) a pak výsledky uloží do úložiště znalostí v Azure. pamì. Pomocí nástrojů jako Power BI nebo Průzkumník služby Storage pak můžete prozkoumat znalostní bázi Knowledge Store.

V tomto článku použijete rozhraní REST API k ingestování, indexování a používání rozšíření AI na sadu pohlídek za Hotel. Recenze hotelu se importují do Azure Blob Storage a výsledky se ukládají jako úložiště znalostí ve službě Azure Table Storage.

Po vytvoření znalostní báze můžete získat informace o přístupu k tomuto znalostnímu obchodu pomocí [Průzkumník služby Storage](knowledge-store-view-storage-explorer.md) nebo [Power BI](knowledge-store-connect-power-bi.md).

## <a name="1---create-services"></a>1\. vytvoření služeb

+ [Vytvořte službu Azure Search](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento kurz můžete použít bezplatnou službu.

+ [Vytvořte účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pro ukládání ukázkových dat a úložiště znalostí. Váš účet úložiště musí pro službu Azure Search používat stejné umístění (například USA – západ). *Druh účtu* musí být *StorageV2 (pro obecné účely v2)* (výchozí) nebo *Storage (pro obecné účely V1)* .

+ Doporučeno: [aplikace po pracovní ploše](https://www.getpostman.com/) pro odesílání požadavků do Azure Search. REST API můžete použít s jakýmkoli nástrojem schopným pracovat s požadavky HTTP a odpověďmi. Pro prozkoumávání rozhraní REST je vhodná volba, která se používá v tomto článku. Kromě toho [zdrojový kód](https://github.com/Azure-Samples/azure-search-postman-searches/Tutorial/Knowledge_Store/KnowledgeStore.postman_collection.json) tohoto článku obsahuje kolekci žádostí post. 

## <a name="2---store-the-data"></a>2\. uložení dat

Načtěte soubor. CSV pro kontrolu hotelu do úložiště objektů BLOB v Azure, aby k němu měl k dispozici Azure Search indexer a mohl by být vydáván prostřednictvím kanálu pro rozšíření AI.

### <a name="create-an-azure-blob-container-with-the-data"></a>Vytvoření kontejneru objektů BLOB v Azure s daty

1. [Stáhněte si data kontroly hotelu uložená v souboru CSV (HotelReviews_Free. csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Tato data pocházejí z Kaggle.com a obsahují názory zákazníků na hotely.
1. [Přihlaste se k Azure Portal](https://portal.azure.com)a přejděte k účtu služby Azure Storage.
1. [Vytvořte kontejner objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Pokud chcete vytvořit kontejner, klikněte v levém navigačním panelu pro svůj účet úložiště na **objekty blob**a pak na panelu příkazů klikněte na **+ kontejner** .
1. Pro nový **název**kontejneru zadejte `hotel-reviews`.
1. Vyberte libovolnou **úroveň veřejného přístupu**. Použili jsme výchozí.
1. Kliknutím na **OK** vytvořte kontejner objektů blob Azure.
1. Otevřete nový kontejner @no__t 0, klikněte na **nahrát**a vyberte soubor **HotelReviews-Free. csv** , který jste si stáhli v prvním kroku.

    ![Nahrání dat]nahrání(media/knowledge-store-create-portal/upload-command-bar.png "pro posuzujecí Hotel")

1. Kliknutím na **nahrát** IMPORTUJTE soubor CSV do Azure Blob Storage. Zobrazí se nový kontejner.

    ![Vytvoření kontejneru objektů BLOB v Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "vytvoření kontejneru objektů blob Azure")

## <a name="3---configure-postman"></a>3\. konfigurace pro post

Stáhnout [zdrojový kód kolekce po](https://github.com/Azure-Samples/azure-search-postman-samples/knowledge-store/KnowledgeStore.postman_collection.json) importu a importovat ho do metody post pomocí **souboru, importovat...** . Přepněte na kartu **kolekce** a klikněte na tlačítko **...** a vyberte **Upravit**. 

![Pozálohovací aplikace ukazující navigaci](media/knowledge-store-create-rest/postman-edit-menu.png "navigovat do nabídky upravit v příspěvku")

V dialogovém okně výsledné Úpravy přejděte na kartu **proměnné** . 

Karta **proměnné** umožňuje přidat hodnoty, které provede, při každém jejich výskytu v rámci dvojitých složených závorek. Například metoda post nahradí symbol `{{admin-key}}` s aktuální hodnotou `admin-key`. Po nahrazení bude tato náhrada uvedena v adresách URL, hlavičkách, textu žádosti a tak dále. 

Hodnotu pro `admin-key` najdete na kartě **klíče** Search Service. Je potřeba změnit `search-service-name` a `storage-account-name` na hodnoty, které jste zvolili v [kroku 1](#1---create-services). Nastavte `storage-connection-string` z hodnoty na kartě **přístupové klíče** účtu úložiště. Ostatní hodnoty, které můžete ponechat beze změny.

(media/knowledge-store-create-rest/postman-variables-window.png "Okno proměnné pro") vystavení ![záložky pro vystavení aplikace]


| Proměnná    | Kde ho získat |
|-------------|-----------------|
| `admin-key` | Search Service, karta **klíče**              |
| `api-version` | Ponechte jako "2019-05-06-Preview" |
| `datasource-name` | Ponechte jako hotelové recenze – DS. | 
| `indexer-name` | Ponechte jako hotelové recenze – IXR. | 
| `index-name` | Ponechejte jako "hotely-recenze-IX" | 
| `search-service-name` | Search Service, hlavní název. Adresa URL je `https://{{search-service-name}}.search.windows.net`. | 
| `skillset-name` | Ponechte jako "hotelové recenze – SS" | 
| `storage-account-name` | Účet úložiště, hlavní název | 
| `storage-connection-string` | Účet úložiště, karta **přístupových klíčů** , **připojovací řetězec** **klíč1** | 
| `storage-container-name` | Ponechte jako hotelové recenze. | 

### <a name="review-the-request-collection-in-postman"></a>Kontrola kolekce požadavků v poli pro odeslání

Vytvoření úložiště znalostí vyžaduje, abyste nastavili čtyři požadavky HTTP: 

1. Požadavek PUT k vytvoření indexu. Tento index obsahuje data, která používá a vrací Azure Search.
1. Požadavek POST k vytvoření zdroje dat. Tento zdroj dat připojí vaše chování Azure Search k účtu úložiště data a úložiště znalostní báze. 
1. Požadavek PUT na vytvoření dovednosti. Dovednosti určuje rozšíření aplikovaná na vaše data a strukturu úložiště znalostí.
1. Požadavek PUT vytvořit indexer Spuštění indexeru čte data, používá dovednosti a ukládá výsledky. Tuto žádost musíte spustit jako poslední.

[Zdrojový kód](https://github.com/Azure-Samples/azure-search-postman-searches/Tutorial/Knowledge_Store/KnowledgeStore.postman_collection.json) obsahuje kolekci post u těchto čtyř požadavků. Pokud chcete tyto požadavky vydat, přepněte na kartě žádosti na kartu žádosti a přidejte `api-key` a `Content-Type` hlavičky žádosti. Nastavte hodnotu `api-key` na `{{admin-key}}`. Nastavte hodnotu `Content-type` na `application/json`. 

> [!div class="mx-imgBorder"]
> @no__t – 0Screenshot znázorňující rozhraní Poster pro záhlaví @ no__t-1

> [!Note]
> V rámci všech vašich požadavků budete muset nastavit @no__t hlaviček-0 a `Content-type`. Pokud je proměnná rozpoznaná, vykreslí se v oranžovém textu jako s `{{admin-key}}` na snímku obrazovky. Pokud je proměnná nesprávně napsaná, vykreslí se červeným textem.
>

## <a name="4---create-an-azure-search-index"></a>4\. vytvoření indexu Azure Search

Je potřeba vytvořit index Azure Search, který bude reprezentovat data, na která vás zajímá hledání, filtrování a provádění vylepšení. Index vytvoříte tak, že vydáte požadavek PUT na `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. Po vypsání budou nahrazeny symboly uzavřené v obou složených závorkách, například `{{search-service-name}}`, `{{index-name}}` a `{{api-version}}` s hodnotami zadanými v [kroku 3](#3---configure-postman). Pokud k vystavování příkazů REST používáte jiný nástroj, budete muset tyto proměnné nahradit sami.

Určete strukturu Azure Search indexu v těle žádosti. V poli po nastavení hlaviček `api-key` a `Content-type` Přepněte do podokna **tělo** žádosti. Měl by se zobrazit následující JSON, ale pokud ne, vyberte **raw** a **JSON (Application/JSON)** a vložte následující kód jako text:

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

Uvidíte, že tato definice indexu je kombinací dat, která byste měli k dispozici uživateli (název hotelu, revize obsahu, data a tak dále), metadat vyhledávání a data pro vylepšení AI (mínění, klíčová fráze a jazyk).

Kliknutím na tlačítko **Odeslat** vydejte požadavek PUT. Měla by se zobrazit stavová zpráva `201 - Created`. Pokud obdržíte jiný stav, v podokně **tělo** se zobrazí odpověď JSON s chybovou zprávou. 

## <a name="5---create-the-datasource"></a>5\. vytvoření zdroje dat

Nyní je třeba připojit Azure Search k datům hotelu, která jste uložili v [kroku 2](#2---store-the-data). Vytvoření zdroje dat se provádí pomocí příspěvku na `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. Znovu budete muset nastavit hlavičky `api-key` a `Content-Type`, jak je uvedeno výše. 

V poli pro odeslání otevřete žádost o vytvoření zdroje dat. Přepněte do podokna **tělo** , které by mělo obsahovat následující kód:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Kliknutím na tlačítko **Odeslat** vydejte žádost post. 

## <a name="6---create-the-skillset"></a>6\. vytvoření dovednosti 

Dalším krokem je zadání dovednosti, které určuje jak vylepšení použít, tak úložiště znalostí, kde se výsledky uloží. V části poslat otevřete kartu vytvořit dovednosti. Tento požadavek pošle vložení do `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`.
Nastavte hlavičky `api-key` a `Content-type`, jak jste to udělali dříve. 

Existují dva velké objekty nejvyšší úrovně: `"skills"` a `"knowledgeStore"`. Každý objekt v objektu `"skills"` je služba rozšíření. Každá služba pro rozšíření má `"inputs"` a `"outputs"`. Všimněte si, jak `LanguageDetectionSkill` má výstupní `targetName` `"Language"`. Hodnota tohoto uzlu je používána většinou dalších dovedností jako vstup se zdrojem jako `document/Language`. Tato možnost použití výstupu jednoho uzlu jako vstupu na jiný je ještě více zřejmá v `ShaperSkill`, která určuje, jak se data budou přecházet do tabulek znalostní báze Store.

Objekt `"knowledge_store"` se připojí k účtu úložiště přes proměnnou post `{{storage-connection-string}}`. Pak obsahuje sadu mapování mezi vylepšeným dokumentem a tabulkami a sloupci, které budou k dispozici v samotném úložišti znalostí. 

Pokud chcete vygenerovat dovednosti, vložte požadavek stisknutím tlačítka **Odeslat** v poli post.

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

## <a name="7---create-the-indexer"></a>7\. vytvoření indexeru

Posledním krokem je vytvoření indexeru, který skutečně čte data a aktivuje dovednosti. V poli pro odeslání přepněte na požadavek "vytvořit indexer" a zkontrolujte text. Jak vidíte, definice indexeru odkazuje na několik dalších prostředků, které jste už vytvořili – zdroj dat, index a dovednosti. 

Objekt `"parameters/configuration"` řídí, jak indexer ingestuje data. V takovém případě jsou vstupní data v jednom dokumentu s řádky záhlaví a hodnotami oddělenými čárkami. Klíč dokumentu je jedinečný identifikátor dokumentu, který před kódováním je adresa URL zdrojového dokumentu. Nakonec jsou výstupní hodnoty dovednosti, jako je kód jazyka, mínění a klíčové fráze, namapovány na příslušné umístění v dokumentu. Všimněte si, že i když existuje jedna hodnota pro `Language`, `Sentiment` se použije u každého prvku v poli `pages`. `Keyphrases` je sama o sobě pole a používá se také pro každý prvek v poli `pages`.

Po nastavení hlaviček `api-key` a `Content-type` a potvrzení, že tělo žádosti je podobné zdrojovému kódu, který následuje, stiskněte **poslat** v poli post. Po odeslání se žádost uloží do `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. Azure Search vytvoří a spustí indexer. 

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

## <a name="8---run-the-indexer"></a>8\. spuštění indexeru 

V Azure Portal přejděte na **přehled** Search Service a vyberte kartu **indexery** . klikněte na položku **hotely-recenze – IXR** , kterou jste vytvořili v předchozím kroku. Pokud indexer ještě není spuštěný, stiskněte tlačítko **Spustit** . Úloha indexování může vyvolávat upozornění týkající se rozpoznávání jazyka, protože data zahrnují některé recenze napsané v jazycích, které ještě nejsou podporované znalostmi rozpoznávání. 

## <a name="next-steps"></a>Další kroky

Teď, když jste rozrostli vaše data pomocí služeb rozpoznávání a prozkoumali výsledky do obchodu Knowledge Store, můžete k prozkoumání obohacené sady dat použít Průzkumník služby Storage nebo Power BI.

Další informace o tom, jak prozkoumat toto úložiště znalostí pomocí Průzkumník služby Storage, najdete v následujícím návodu.

> [!div class="nextstepaction"]
> [Zobrazit s Průzkumník služby Storage](knowledge-store-view-storage-explorer.md)

Další informace o tom, jak připojit toto úložiště znalostní báze k Power BI, najdete v následujícím návodu.

> [!div class="nextstepaction"]
> [Propojení s Power BI](knowledge-store-connect-power-bi.md)

Pokud chcete tento cvičení opakovat nebo si vyzkoušet jiný návod k rozšíření AI, odstraňte indexer *idxr hotelového přezkoumání* . Odstranění indexeru obnoví čítač bezplatných denních transakcí zpátky na nulu.
