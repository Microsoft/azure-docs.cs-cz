---
title: Jak začít pracovat s znalostní báze Store (preview) – Azure Search
description: Přečtěte si postup pro odeslání bohatších možností dokumentů vytvořil AI indexování kanály ve službě Azure Search do znalostní báze úložiště v účtu úložiště Azure. Odtud můžete zobrazit, upravovat a využívat bohatších možností dokumentů ve službě Azure Search i v jiných aplikacích.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: heidist
ms.openlocfilehash: d9006e3fcfc9691b9f3eec4b86c545fd3fea9f8a
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471741"
---
# <a name="how-to-get-started-with-knowledge-store"></a>Jak začít pracovat s Store znalostní báze

[Znalostní báze Store](knowledge-store-concept-intro.md) je nová funkce ve verzi preview ve službě Azure Search, která ukládá obohacení AI vytvořené v kanál indexování pro dolování znalostní báze v jiných aplikacích. Uložené obohacení můžete také použít k pochopení a zpřesnit kanál indexování Azure Search.

Znalostní báze úložiště je definována dovedností. Pro scénáře fulltextové vyhledávání regulárního Azure Search účel dovedností poskytuje obohacení AI, aby obsah více možností prohledávání. Pro scénáře dolování znalostní báze role dovedností je vytváření, sestavování a uložení několika datových struktur pro analýzu nebo modelování v jiných aplikací a procesů.

V tomto cvičení začněte ukázková data, služby a nástroje pro další základní pracovní postup pro vytváření a používání vaší první znalostní báze úložiště, s důrazem na definici dovednosti.

## <a name="prerequisites"></a>Požadavky

Následující služby, nástroje a data se používají v tomto rychlém startu. 

+ [Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službu pro účely tohoto kurzu. 

+ [Vytvoření účtu služby Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pro ukládání ukázková data. Vaše znalosti úložiště bude existovat ve službě Azure storage. 

+ [Vytvoření prostředku služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) S0 s průběžnými platbami úrovní broad-spectrum přístup k široké spektrum dovednosti používaných pro obohacení AI. Tento prostředek a služby Azure Search se musí být ve stejné oblasti.

+ [Desktopová aplikace postman](https://www.getpostman.com/) pro odesílání požadavků do služby Azure Search.

+ [Kolekce postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Caselaw) připravené požadavky pro vytvoření zdroje dat, index, dovednosti a indexer. Několik definic objektů jsou příliš dlouhé mají být zahrnuty v tomto článku. Musíte získat tuto kolekci zobrazíte definic indexu a dovedností v plné výši.

+ [Ukázková data judikatura](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) pocházející z [judikatura přístup k projektu](https://case.law/bulk/download/) stránce pro stažení veřejného hromadných dat. Konkrétně na konci cvičení používá prvních 10 dokumenty prvního stahování (Bosna a Hercegovina). Ukázka 10 dokumentu jsme nahráli do Githubu pro účely tohoto cvičení.

## <a name="get-a-key-and-url"></a>Získejte klíč a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/)a ve vyhledávací službě **přehled** stránce, získat adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V **nastavení** > **klíče**, získat klíč pro úplná práva správce na službu. Existují dva klíče zaměnitelné správce, v případě, že budete potřebovat k výměně jeden k dispozici zajišťuje nepřetržitý chod podniků. U požadavků můžete použít buď primární nebo sekundární klíč pro přidání, úpravy a odstraňování objektů.

    ![Získejte koncový bod a přístupový klíč rozhraní HTTP](media/search-fiddler/get-url-key.png "získat HTTP koncový bod a přístupový klíč")

Všechny požadavky vyžaduje klíč rozhraní api na každou požadavku odeslaného do vaší služby.

## <a name="prepare-sample-data"></a>Příprava ukázkových dat

1. [Přihlaste se k webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště Azure, klikněte na tlačítko **objekty BLOB**a potom klikněte na tlačítko **+ kontejner**.

1. [Vytvořte kontejner objektů Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) obsahuje ukázková data. Použijte kontejner název "judikatura test". Můžete nastavit úroveň veřejného přístupu k některému z jeho platných hodnot.

1. Po vytvoření kontejneru ho otevřete a vyberte **nahrát** na panelu příkazů.

   ![Nahrát na panelu příkazů](media/search-semi-structured-data/upload-command-bar.png "nahrát na panelu příkazů")

1. Přejděte do složky obsahující **judikatura sample.json** ukázkový soubor. Vyberte soubor a potom klikněte na tlačítko **nahrát**.


## <a name="set-up-postman"></a>Nastavení nástroje Postman

Spusťte Postman a import kolekce Postman judikatura. Můžete také nastavte sérii požadavků HTTP. Pokud tento nástroj neznáte, přečtěte si téma [prozkoumejte službu Search REST API služby Azure pomocí nástroje Postman](search-fiddler.md).

+ Metoda požadavku pro všechna volání v tomto návodu je **UMÍSTIT** nebo **příspěvek**.
+ Hlavičky žádosti (2) zahrnují následující: "Content-type" nastavena na "application/json", "klíč api-key" nastavena na klíč"admin" (klíč správce je zástupný symbol pro váš primární klíč služby search) v uvedeném pořadí. 
+ Text žádosti je třeba umístit samotný obsah volání. 

  ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/postmanoverview.png)

Pomocí nástroje Postman budeme provádět čtyři volání rozhraní API do služby search vytváření zdroje dat, index, dovedností a indexer – v tomto pořadí. Zdroj dat obsahuje ukazatel na váš účet úložiště Azure a dat JSON. Služby search se připojí při importu dat.

[Vytvoření dovedností](#create-skillset) je hlavním cílem tohoto návodu: Určuje obohacování kroky a jak se data ukládají v úložišti znalostní báze.

Koncový bod adresy URL musíte zadat verzi rozhraní api a každé volání by měla vrátit **201 – vytvořeno**. Ve verzi preview api-version pro vytvoření dovedností s podporou znalostní báze úložiště je `2019-05-06-Preview` (malá a velká písmena).

Spusťte následující volání rozhraní API z klienta REST.

## <a name="create-a-data-source"></a>Vytvoření zdroje dat

[Rozhraní API vytvořit zdroj dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) vytvoří objekt, který určuje, jaká data do indexu Azure Search.

Koncový bod tohoto volání je `https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview` 

1. Nahraďte `[service name]` názvem vaší služby Search. 

2. Pro toto volání text požadavku musí obsahovat název svého účtu úložiště připojovací řetězec a objektů blob v kontejneru. Připojení můžete najít na webu Azure Portal v účtu úložiště **přístupové klíče**. 

   Nezapomeňte nahradit název připojení řetězec a objektů blob v kontejneru v textu požadavku před provedením volání.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<YOUR-BLOB-CONTAINER-NAME>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

3. Odešlete požadavek. Odpověď by měla být **201** a tělo odpovědi by měl vypadat téměř stejný jako datová část požadavku, které jste zadali.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="create-an-index"></a>Vytvoření indexu
    
Druhé volání je [vytvořit Index API](https://docs.microsoft.com/rest/api/searchservice/create-data-source), vytvoření indexu Azure Search, která ukládá veškerá prohledávatelná data. Index určuje pole, parametry a atributy.

Není nezbytně nutné index pro znalostní báze dolování, ale indexeru se nespustí, pokud je k dispozici indexu. 

Adresa URL pro toto volání je `https://[service name].search.windows.net/indexes?api-version=2019-05-06-Preview`

1. Nahraďte `[service name]` názvem vaší služby Search.

2. Zkopírujte definici indexu ze žádosti o vytvoření indexu v kolekci Postman do textu žádosti. Definice indexu je několik stovek řádků k vytištění zde příliš dlouhý. 

   Vnější prostředí indexu se skládá z následujících elementů. 

   ```json
   {
      "name": "caselaw",
      "defaultScoringProfile": null,
      "fields": [],
      "scoringProfiles": [],
      "corsOptions": null,
      "suggesters": [],
      "analyzers": [],
      "tokenizers": [],
      "tokenFilters": [],
      "charFilters": [],
      "encryptionKey": null
   }
   ```

3. `fields` Kolekce obsahuje hromadně definici indexu. Zahrnuje jednoduchou pole [komplexní pole](search-howto-complex-data-types.md) vnořené používání dílčích struktur a kolekce.

   Zkontrolujte definici pole pro `casebody` na řádky 302-384. Všimněte si, že komplexní pole může obsahovat jiné komplexní pole potřeby Hierarchická reprezentace.

   ```json
   {
    "name": "casebody",
    "type": "Edm.ComplexType",
    "fields": [
        {
            "name": "status",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": true,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "data",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "head_matter",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "retrievable": true,
                    "sortable": false,
                    "facetable": false,
                    "key": false,
                    "indexAnalyzer": null,
                    "searchAnalyzer": null,
                    "analyzer": null,
                    "synonymMaps": []
                },
                {
                    "name": "opinions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "author",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": false,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "type",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        }
                    ]
                },
    . . .
   ```

4. Odešlete požadavek. 

   Odpověď by měla být **201** a vypadat podobně jako v následujícím příkladu první několik polí:

    ```json
    {
        "name": "caselaw",
        "defaultScoringProfile": null,
        "fields": [
            {
                "name": "id",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": true,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "name",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
      . . .
    ```

<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Vytvoření úložiště dovednosti a znalosti

[Vytvořit rozhraní API dovednosti](https://docs.microsoft.com/rest/api/searchservice/create-skillset) vytvoří objekt Azure Search, která určuje, jaké kognitivní dovednosti k volání, jak zřetězit dohromady a co je nejdůležitější dovednosti pro Tento názorný postup – určení úložiště znalostní báze.

Koncový bod tohoto volání je `https://[service name].search.windows.net/skillsets?api-version=2019-05-06-Preview`

1. Nahraďte `[service name]` názvem vaší služby Search.

2. Zkopírujte definici dovednosti z kolekce Postman do textu žádosti na požadavek na vytvoření dovedností. Definice znalostí je několik stovek řádků příliš dlouhý pro tisk, ale je hlavním cílem tohoto návodu.

   Vnější prostředí dovedností se skládá z následujících elementů. `skills` Kolekce definuje obohacení v paměti, ale `knowledgeStore` definic Určuje, jak se ukládá výstup. `cognitiveServices` Definice není připojení k obohacení moduly AI.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

3. Nejprve nastavte `cognitiveServices` a `knowledgeStore` klíče a připojovací řetězec. V tomto příkladu tyto řetězce jsou umístěné za definici dovednosti, na konci textu požadavku. Použijte prostředek služeb Cognitive Services, zřízené na úroveň S0, umístěný ve stejné oblasti jako Azure Search.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

3. Zkontrolujte kolekce dovednosti, zejména Shaper dovednosti na řádcích 85 a 170, v uvedeném pořadí. Dovednosti Shaper je důležité, protože sestavuje datové struktury, která chcete pro dolování znalostní báze. Během provádění dovednosti tyto struktury jsou pouze v paměti, ale jak přesunete k dalšímu kroku, se zobrazí, jak lze tento výstup uložit do úložiště znalostní báze pro další zkoumání.

   Následující fragment kódu je z řádku 217. 

    ```json
    "name": "Opinions",
    "source": null,
    "sourceContext": "/document/casebody/data/opinions/*",
    "inputs": [
        {
            "name": "Text",
            "source": "/document/casebody/data/opinions/*/text"
        },
        {
            "name": "Author",
            "source": "/document/casebody/data/opinions/*/author"
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/casebody/data/opinions/*/text/pages/*/entities/*",
            "inputs": [
                {
                    "name": "Entity",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/value"
                },
                {
                    "name": "EntityType",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/category"
                }
            ]
        }
    ]
   . . .
   ```

3. Zkontrolujte `projections` prvek `knowledgeStore`začíná na řádku 262. Projekce zadejte úložiště složení znalostní báze. Projekce jsou určené v dvojice tabulky objektů, ale aktuálně pouze jednu v době. Jak je vidět v první projekci `tables` je zadán, ale `objects` není. V druhém je opak.

   Ve službě Azure storage vytvoří se tabulky ve službě Table storage pro každou tabulku, kterou jste vytvořili a každý objekt získá kontejner v úložišti objektů Blob.

   Objekty BLOB obvykle obsahují úplného výrazu rozšíření. Tabulky obvykle obsahují částečnou obohacení v kombinacích, které můžete uspořádat pro konkrétní účely. Tento příklad ukazuje případů tabulku a tabulku s názory, ale nezobrazují se další tabulky jako entity, Nedopustíte, porotce a strany.

    ```json
    "projections": [
        {
            "tables": [
                {
                    "tableName": "Cases",
                    "generatedKeyName": "CaseId",
                    "source": "/document/Case"
                },
                {
                    "tableName": "Opinions",
                    "generatedKeyName": "OpinionId",
                    "source": "/document/Case/OpinionsSnippets/*"
                }
            ],
            "objects": []
        },
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "enrichedcases",
                    
                    "source": "/document/CaseFull"
                }
            ]
        }
    ]
    ```

5. Odešlete požadavek. Odpověď by měla být **201** a vypadat podobně jako v následujícím příkladu zobrazuje první část odpovědi.

    ```json
    {
    "name": "caselaw-ss",
    "description": null,
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "SplitSkill#1",
            "description": null,
            "context": "/document/casebody/data/opinions/*/text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/casebody/data/opinions/*/text
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        . . .
    ```

## <a name="create-and-run-an-indexer"></a>Vytvoření a spuštění indexeru

[Vytvořit Indexer API](https://docs.microsoft.com/rest/api/searchservice/create-indexer) vytvoří a spustí okamžitě indexeru. Všechny definice, kterou jste vytvořili, pokud jsou vloženy do pohybu s tímto krokem. Spuštění indexeru je okamžitě, protože neexistuje ve službě. Existuje, volání POST do existujícího indexeru po operaci aktualizace.

Koncový bod tohoto volání je `https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview`

1. Nahraďte `[service name]` názvem vaší služby Search. 

2. Pro toto volání tělo požadavku určuje název indexeru. Zdroj dat a index jsou vyžadované indexeru. Dovedností je volitelný pro indexer, ale vyžaduje pro rozšiřování AI.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/organizations/*",
                "targetFieldName": "orginizations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/locations/*",
                "targetFieldName": "locations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/Case/OpinionsSnippets/*/Entities/*",
                "targetFieldName": "entities",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/keyPhrases/*",
                "targetFieldName": "keyPhrases",
                "mappingFunction": null
            }
        ]
    }
    ```

3. Odešlete požadavek. Odpověď by měla být **201** a tělo odpovědi by měl vypadat téměř stejný jako datová část požadavku jste zadali (oříznut pro zkrácení).

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            }
        ]
    }
    ```

## <a name="explore-knowledge-store"></a>Prozkoumejte službu knowledge store

Můžete začít s prozkoumáváním jako první dokument se importují. Pro tento úkol použijte [ **Průzkumníka služby Storage** ](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer) na portálu.

Je důležité si uvědomit, že je úložiště znalostní báze plně odpojen od Azure Search. Index Azure Search a znalostní báze úložišti oba obsahují reprezentaci dat a obsah, ale část způsoby, jak z něj. Pomocí rejstříku pro fulltextové vyhledávání, vyhledávání filtrované a všechny scénáře podporované ve službě Azure Search. Nebo Posunout vpřed s právě znalostní báze úložišti, připojení dalších nástrojů k analýze obsahu.

## <a name="takeaways"></a>Shrnutí

Právě jste vytvořili vaší první znalostní báze úložiště ve službě Azure storage a slouží k zobrazení obohacení Průzkumníka služby Storage. Toto je základní prostředí pro práci s obohacení uložené. 

## <a name="next-steps"></a>Další postup

Dovednosti Shaper provede rutinní týkající se vytvoření detailní data formuláře, které je možné kombinovat do nové obrazce. V dalším kroku zkontrolujte na referenční stránce tento dovednosti pro podrobnosti o způsobu jejich použití.

> [!div class="nextstepaction"]
> [Odkaz na Shaper dovedností](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->