---
title: Jak chcete začít s znalostní báze dolování (preview) – Azure Search
description: Přečtěte si postup pro odeslání bohatších možností dokumentů vytvořil AI indexování kanály ve službě Azure Search do znalostní báze úložiště v účtu úložiště Azure. Odtud můžete zobrazit, upravovat a využívat bohatších možností dokumentů ve službě Azure Search i v jiných aplikacích.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 06/29/2019
ms.author: heidist
ms.openlocfilehash: e50dfcdc5ac2fbe2435066546a340874e1b8f682
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551056"
---
# <a name="how-to-get-started-with-knowledge-mining-in-azure-search"></a>Jak začít pracovat s znalostní báze dolování ve službě Azure Search

> [!Note]
> Znalostní báze úložiště je ve verzi preview a není určen pro použití v produkčním prostředí. [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) tuto funkci poskytuje. Není dostupná podpora .NET SDK v současnosti.
>
[Znalostní báze úložiště](knowledge-store-concept-intro.md) uloží obohaceného AI dokumenty vytvořené při indexování do účtu úložiště Azure pro příjem dat znalostní báze dolování v jiných aplikacích. Uložené obohacení můžete také použít k pochopení a zpřesnit kanál indexování Azure Search. 

Znalostní báze úložiště je definován *dovednosti* a vytvořené *indexer*. Je zadán fyzický výraz znalostní báze úložiště prostřednictvím *projekce* , které definují datové struktury v úložišti. Čas dokončení tohoto návodu bude vytvořená všechny tyto objekty a budete vědět, jak všechny zapadají. 

V tomto cvičení začněte ukázková data, služby a nástroje pro další základní pracovní postup pro vytváření a používání vaší první znalostní báze úložiště, s důrazem na definici dovednosti.

## <a name="prerequisites"></a>Požadavky

Znalostní báze úložiště je uprostřed více služeb, s Azure Blob storage a Azure Table storage poskytuje fyzického úložiště a Azure Search a Cognitive Services pro vytvoření objektu a aktualizací. Znalost [základní architekturu](knowledge-store-concept-intro.md) je předpokladem pro tento návod.

Tyto služby a nástroje se používají v tomto rychlém startu. 

+ [Získat desktopová aplikace Postman](https://www.getpostman.com/), která se používá pro odesílání požadavků HTTP do služby Azure Search.

+ [Vytvoření účtu služby Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pro uložení ukázkových dat a ve znalostní bázi úložiště. Vaše znalosti úložiště bude existovat ve službě Azure storage.

+ [Vytvoření prostředku služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) S0 s průběžnými platbami úrovní broad-spectrum přístup k široké spektrum dovednosti používaných pro obohacení AI. Služby cognitive Services a služby Azure Search se musí být ve stejné oblasti.

+ [Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službu pro účely tohoto kurzu. 

Vyžaduje se také ukázkové dokumenty JSON a soubor Postman collection. Pokyny k vyhledání a načtení doplňující souborů je součástí [Příprava ukázková data](#prepare-sample-data) oddílu.

## <a name="get-a-key-and-url"></a>Získejte klíč a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/)a ve vyhledávací službě **přehled** stránce, získat adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V **nastavení** > **klíče**, získat klíč pro úplná práva správce na službu. Existují dva klíče zaměnitelné správce, v případě, že budete potřebovat k výměně jeden k dispozici zajišťuje nepřetržitý chod podniků. U požadavků můžete použít buď primární nebo sekundární klíč pro přidání, úpravy a odstraňování objektů.

    ![Získejte koncový bod a přístupový klíč rozhraní HTTP](media/search-get-started-postman/get-url-key.png "získat HTTP koncový bod a přístupový klíč")

Všechny požadavky vyžaduje klíč rozhraní api na každou požadavku odeslaného do vaší služby. Název služby a klíč API-key budete poskytovat v každém požadavku HTTP v následujících částech.

<a name="prepare-sample-data"></a>

## <a name="prepare-sample-data"></a>Příprava ukázkových dat

Znalostní báze úložiště obsahuje výstup kanál rozšíření. Vstupy se skládají z "nepoužitelné" data se nakonec stanou "použitelné" při jejich postupu prostřednictvím kanálu. Příklady nepoužitelnými daty může obsahovat soubory obrázků, které musí být analyzován na text nebo obrázek vlastnosti nebo hustému textové soubory, které mohou být analyzovány pro entity, klíčové fráze a mínění. 

Toto cvičení používá hustému textové soubory (informace o případu práva), které mohou být [judikatura přístup k projektu](https://case.law/bulk/download/) veřejná Data hromadné stažení stránky. Ukázka 10 dokumentu jsme nahráli do Githubu pro účely tohoto cvičení. 

V této úloze vytvoříte kontejner objektů Blob v Azure pro tyto dokumenty, který se použije jako vstup do kanálu. 

1. Stažení a extrakci [Azure Search ukázková Data](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) úložišti, abyste si [judikatura datovou sadu](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw). 

1. [Přihlaste se k webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště Azure, klikněte na tlačítko **objekty BLOB**a potom klikněte na tlačítko **+ kontejner**.

1. [Vytvořte kontejner objektů Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) obsahuje ukázková data: 

   1. Název kontejneru `caselaw-test`. 
   
   1. Nastavte úroveň veřejného přístupu na některou z jeho platných hodnot.

1. Po vytvoření kontejneru ho otevřete a vyberte **nahrát** na panelu příkazů.

   ![Nahrát na panelu příkazů](media/search-semi-structured-data/upload-command-bar.png "nahrát na panelu příkazů")

1. Přejděte do složky obsahující **judikatura sample.json** ukázkový soubor. Vyberte soubor a potom klikněte na tlačítko **nahrát**.

1. Když jste ve službě Azure storage, získejte název připojovacího řetězce a kontejner.  Budete potřebovat oba z těchto řetězců v [vytvořit zdroj dat](#create-data-source):

   1. Na stránce Přehled klikněte na tlačítko **přístupové klíče** a zkopírujte *připojovací řetězec*. Začíná `DefaultEndpointsProtocol=https;` a končí `EndpointSuffix=core.windows.net`. Název účtu a klíč jsou mezi. 

   1. Název kontejneru musí být `caselaw-test` nebo jakýmkoli názvem jste přiřadili.



## <a name="set-up-postman"></a>Nastavení nástroje Postman

Postman je klientská aplikace, které budete používat k odesílání požadavků a dokumentů JSON do Azure Search. Některé z požadavků se dají formulovat pomocí pouze informace v tomto článku. Dvě největší požadavků (vytvoření indexu, vytváření dovedností) ale obsahovat podrobné JSON, který je příliš velká k vložení do článku. 

Chcete-li všechny dokumenty JSON a požadavky plně dostupná, jsme vytvořili soubor Postman collection. Stažení a následným importem tohoto souboru je vaší první úlohou nastavení klienta.

1. Stáhněte a rozbalte [ukázek Azure Search Postman](https://github.com/Azure-Samples/azure-search-postman-samples) úložiště.

1. Spusťte Postman a import kolekce Postman judikatura:

   1. Klikněte na tlačítko **Import** > **importovat soubory** > **vybrat soubory**. 

   1. Přejděte do složky \azure-search-postman-samples-master\azure-search-postman-samples-master\Caselaw.

   1. Select **Caselaw.postman_collection_v2.json**. Měli byste vidět čtyři **příspěvek** požadavků v kolekci.

   ![Kolekce postman pro ukázku judikatura](media/knowledge-store-howto/postman-collection.png "kolekce Postman pro ukázku judikatura")
   

## <a name="create-an-index"></a>Vytvoření indexu
    
První požadavek používá [vytvořit Index API](https://docs.microsoft.com/rest/api/searchservice/create-data-source), vytvoření indexu Azure Search, která ukládá veškerá prohledávatelná data. Index určuje pole, parametry a atributy.

Není nezbytně nutné index pro znalostní báze dolování, ale indexeru se nespustí, pokud je k dispozici indexu. 

1. V adrese URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexes?api-version=2019-05-06-Preview`, nahraďte `YOUR-AZURE-SEARCH-SERVICE-NAME` s názvem vaší služby search. 

1. V záhlaví, nahraďte `<YOUR AZURE SEARCH ADMIN API-KEY>` s klíčem rozhraní API pro správu pro službu Azure Search.

1. V části textu dokumentu JSON je schématu indexu. Sbalení pro viditelnost vnější prostředí indexu se skládá z následujících elementů. Kolekce polí odpovídající pole v datové sadě judikatura.

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

1. Rozbalte `fields` kolekce. Obsahuje hromadně definici indexu, která se skládá z jednoduchého pole [komplexní pole](search-howto-complex-data-types.md) vnořené používání dílčích struktur a kolekce.

   Za chvíli Zkontrolujte definici pole pro `casebody` komplexní pole na řádcích 302-384. Všimněte si, že komplexní pole může obsahovat jiné komplexní pole potřeby Hierarchická reprezentace. Hierarchické struktury můžete modelovat v indexu, jak je znázorněno zde a také jako projekce dovedností, tedy vytvoření vnořené struktury dat v úložišti znalostní báze.

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

1. Klikněte na tlačítko **odeslat** provedení požadavku.  Měli byste obdržet **stavu: 201 – vytvořeno** zprávu jako odpověď.

<a name="create-data-source"></a>

## <a name="create-a-data-source"></a>Vytvoření zdroje dat

Druhý požadavek používá [rozhraní API vytvořit zdroj dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) pro připojení k úložišti objektů Blob v Azure. 

1. V adrese URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/datasources?api-version=2019-05-06-Preview`, nahraďte `YOUR-AZURE-SEARCH-SERVICE-NAME` s názvem vaší služby search. 

1. V záhlaví, nahraďte `<YOUR AZURE SEARCH ADMIN API-KEY>` s klíčem rozhraní API pro správu pro službu Azure Search.

1. V části textu dokument JSON obsahuje název svého účtu úložiště připojovací řetězec a objektů blob v kontejneru. Připojovací řetězec najdete na webu Azure Portal v účtu úložiště **přístupové klíče**. 

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

1. Klikněte na tlačítko **odeslat** provedení požadavku.  Měli byste obdržet **stavu: 201 – vytvořeno** zprávu jako odpověď.



<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Vytvoření úložiště dovednosti a znalosti

Třetí požadavek používá [vytvořit rozhraní API dovednosti](https://docs.microsoft.com/rest/api/searchservice/create-skillset), vytvoření objektu Azure Search, která určuje, jaké kognitivní dovednosti k volání, jak zřetězit dohromady a co je nejdůležitější dovednosti pro Tento názorný postup – určení úložiště znalostní báze.

1. V adrese URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/skillsets?api-version=2019-05-06-Preview`, nahraďte `YOUR-AZURE-SEARCH-SERVICE-NAME` s názvem vaší služby search. 

1. V záhlaví, nahraďte `<YOUR AZURE SEARCH ADMIN API-KEY>` s klíčem rozhraní API pro správu pro službu Azure Search.

1. V části textu je dokument JSON definice dovednosti. Sbalení pro viditelnost vnější prostředí dovedností se skládá z následujících elementů. `skills` Kolekce definuje obohacení v paměti, ale `knowledgeStore` definic Určuje, jak se ukládá výstup. `cognitiveServices` Definice není připojení k obohacení moduly AI.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

1. Rozbalte `cognitiveServices` a `knowledgeStore` může poskytnout informace o připojení. V tomto příkladu tyto řetězce jsou umístěné za definici dovednosti, na konci textu požadavku. 

   Pro `cognitiveServices`, zřídit prostředek na úroveň S0, umístěný ve stejné oblasti jako Azure Search. CognitiveServices názvu a klíče můžete získat na stejné stránce na webu Azure Portal. 
   
   Pro `knowledgeStore`, můžete použít stejný připojovací řetězec použitý pro kontejner objektů Blob judikatura.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

1. Rozbalte kolekci dovednosti zejména Shaper dovednosti na řádcích 85 a 179, v uvedeném pořadí. Dovednosti Shaper je důležité, protože sestavuje datové struktury, která chcete pro dolování znalostní báze. Během provádění dovednosti tyto struktury jsou pouze v paměti, ale jak přesunete k dalšímu kroku, se zobrazí, jak lze tento výstup uložit do úložiště znalostní báze pro další zkoumání.

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

1. Rozbalte `projections` prvek `knowledgeStore`začíná na řádku 262. Projekce zadejte úložiště složení znalostní báze. Projekce jsou určené v dvojice tabulky objektů, ale aktuálně pouze jednu v době. Jak je vidět v první projekci `tables` je zadán, ale `objects` není. V druhém je opak.

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

1. Klikněte na tlačítko **odeslat** provedení požadavku. Odpověď by měla být **201** a vypadat podobně jako v následujícím příkladu zobrazuje první část odpovědi.

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

Čtvrtá žádost používá [vytvořit Indexer API](https://docs.microsoft.com/rest/api/searchservice/create-indexer), vytvoření indexeru Azure Search. Indexer je prováděcí modul na kanál indexování. Všechny definice, kterou jste vytvořili, pokud jsou vloženy do pohybu s tímto krokem.

1. V adrese URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexers?api-version=2019-05-06-Preview`, nahraďte `YOUR-AZURE-SEARCH-SERVICE-NAME` s názvem vaší služby search. 

1. V záhlaví, nahraďte `<YOUR AZURE SEARCH ADMIN API-KEY>` s klíčem rozhraní API pro správu pro službu Azure Search.

1. V části textu dokumentu JSON Určuje název indexeru. Zdroj dat a index jsou vyžadované indexeru. Dovedností je volitelný pro indexer, ale vyžaduje pro rozšiřování AI.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": { },
        "fieldMappings": [],
        "outputFieldMappings": [ ]
    ```

1. Rozbalte outputFieldMappings. Rozdíl od fieldMappings, které se používají pro vlastní mapování mezi poli ve zdroji dat a polí v indexu, jsou outputFieldMappings použitý pro mapování polí bohatších možností, vytvořeny a naplněny kanál, pro výstupní pole v indexu, projekce.

    ```json
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
    ```

1. Klikněte na tlačítko **odeslat** provedení požadavku. Odpověď by měla být **201** a tělo odpovědi by měl vypadat téměř stejný jako datová část požadavku jste zadali (oříznut pro zkrácení).

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": { },
        "fieldMappings": [],
        "outputFieldMappings": [ ]
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
