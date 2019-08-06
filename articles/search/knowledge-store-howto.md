---
title: Jak začít s úložištěm Knowledge Store (Preview) – Azure Search
description: Naučte se, jak odesílat obohacené dokumenty vytvořené kanály indexování AI v Azure Search do úložiště znalostí v účtu Azure Storage. Odtud můžete zobrazit, změnit tvar a využívat obohacené dokumenty v Azure Search i v jiných aplikacích.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: tutorial
ms.date: 06/29/2019
ms.author: heidist
ms.openlocfilehash: 139cb598ee774e52da8d97e8d209e11ec4ffc94d
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828449"
---
# <a name="how-to-get-started-with-knowledge-store-in-azure-search"></a>Jak začít se službou Knowledge Store v Azure Search

> [!Note]
> Znalostní databáze je ve verzi Preview a není určená pro produkční použití. Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není dostupná žádná podpora sady .NET SDK.
>
[Znalostní báze](knowledge-store-concept-intro.md) šetří soubory AI, které se vytvořily během indexování vašeho účtu Azure Storage pro dolování dat v jiných aplikacích. Pomocí uložených rozšíření můžete také pochopit a zdokonalit Azure Search kanál indexování. 

Znalostní databáze je definována *dovednosti* a vytvořeným *indexerem*. Fyzický výraz úložiště znalostí je určen pomocí projekcí, které určují datové struktury v úložišti. V době, kdy jste tento návod dokončili, jste vytvořili všechny tyto objekty a vy budete znát, jak se budou všechny vzájemně přizpůsobovat. 

V tomto cvičení začněte s ukázkovými daty, službami a nástroji a Naučte se základní pracovní postup pro vytváření a používání vašeho prvního obchodu s poznatky s důrazem na dovednosti definition.

## <a name="prerequisites"></a>Požadavky

Znalostní báze je ve středu více služeb, kde Azure Blob Storage a Azure Table Storage poskytuje fyzické úložiště a Azure Search a Cognitive Services pro vytváření a aktualizace objektů. Znalost [základní architektury](knowledge-store-concept-intro.md) je předpokladem pro tento návod.

V tomto rychlém startu se používají následující služby a nástroje. 

+ [Získat aplikaci po pracovní ploše](https://www.getpostman.com/), která se používá k odesílání požadavků HTTP do Azure Search.

+ [Vytvořte účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pro ukládání ukázkových dat a úložiště znalostí. Vaše znalostní báze bude existovat v úložišti Azure.

+ [Vytvořte prostředek Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) na úrovni průběžných plateb podle aktuálního využití S0 pro širokou škálu dovedností, která se používá v rozšíření AI. Cognitive Services a vaše služba Azure Search musí být ve stejné oblasti.

+ [Vytvořte službu Azure Search](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento kurz můžete použít bezplatnou službu. 

Jsou také požadovány ukázkové dokumenty JSON a soubor kolekce po odeslání. Pokyny pro vyhledání a načtení doplňkových souborů jsou uvedené v části [Příprava ukázkových dat](#prepare-sample-data) .

## <a name="get-a-key-and-url"></a>Získat klíč a adresu URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. Přihlaste se [k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení** > **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

    ![Získání koncového bodu http a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu http a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. V následujících oddílech zadáte název služby a klíč API-Key v každém požadavku HTTP.

<a name="prepare-sample-data"></a>

## <a name="prepare-sample-data"></a>Příprava ukázkových dat

Znalostní databáze obsahuje výstup kanálu rozšíření. Vstupy se skládají z "nepoužitelných" dat, která se při průběhu kanálu stane příznakem "použitelného". Příklady nepoužitelných dat mohou zahrnovat soubory obrázků, které je třeba analyzovat pro charakteristiky textu nebo obrázku, nebo zhuštěné textové soubory, které lze analyzovat pro entity, klíčové fráze nebo mínění. 

Toto cvičení používá zhuštěné textové soubory (informace o právním řádu), které pocházejí ze stránky pro stažení veřejné hromadné služby pro [přístup k projektu Caselaw](https://case.law/bulk/download/) . Pro toto cvičení jsme nahráli ukázku pro 10 dokumentů na GitHub. 

V této úloze vytvoříte kontejner objektů BLOB v Azure pro tyto dokumenty, které použijete jako vstup do kanálu. 

1. Stáhněte si a rozbalte úložiště [ukázkových dat Azure Search](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) , abyste získali [datovou sadu Caselaw](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw). 

1. Přihlaste se [k Azure Portal](https://portal.azure.com), přejděte k účtu úložiště Azure, klikněte na **objekty blob**a pak klikněte na **+ kontejner**.

1. [Vytvořte kontejner objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) , který bude obsahovat vzorová data: 

   1. Pojmenujte `caselaw-test`kontejner. 
   
   1. Nastavte úroveň veřejného přístupu na jakoukoli z jeho platných hodnot.

1. Po vytvoření kontejneru ho otevřete a na panelu příkazů vyberte **nahrát** .

   ![Nahrát na panel příkazů](media/search-semi-structured-data/upload-command-bar.png "Nahrát na panel příkazů")

1. Přejděte do složky, která obsahuje vzorový soubor **caselaw-Sample. JSON** . Vyberte soubor a pak klikněte na **nahrát**.

1. Když jste ve službě Azure Storage, Získejte připojovací řetězec a název kontejneru.  Oba tyto řetězce budete potřebovat v části [vytvořit zdroj dat](#create-data-source):

   1. Na stránce Přehled klikněte na **přístupové klíče** a zkopírujte *připojovací řetězec*. Začíná `DefaultEndpointsProtocol=https;` a končí `EndpointSuffix=core.windows.net`. Název vašeho účtu a klíč jsou mezi. 

   1. Název kontejneru by měl být `caselaw-test` nebo libovolný název, který jste přiřadili.



## <a name="set-up-postman"></a>Nastavení nástroje Postman

Odesílací je klientská aplikace, kterou použijete k odeslání žádostí a dokumentů JSON pro Azure Search. Několik požadavků lze formulovat pouze pomocí informací v tomto článku. Nicméně dvě z největších požadavků (vytvoření indexu, vytvoření dovednosti) obsahují podrobný formát JSON, který je příliš velký pro vložení do článku. 

Aby všechny dokumenty JSON a požadavky byly plně dostupné, vytvořili jsme soubor kolekce post. Stažení a import tohoto souboru je první úloha při nastavování klienta.

1. Stáhněte si a rozbalte úložiště [ukázek Azure Search](https://github.com/Azure-Samples/azure-search-postman-samples) .

1. Spusťte post a importujte kolekci Caselaw post:

   1. Klikněte na **importovat** > **Import souborů** > **zvolit soubory**. 

   1. Přejděte do složky \azure-search-postman-samples-master\azure-search-postman-samples-master\Caselaw.

   1. Vyberte **Caselaw. postman_collection_v2. JSON**. V kolekci by se měly zobrazit čtyři požadavky **post** .

   ![Kolekce post pro Caselaw ukázku](media/knowledge-store-howto/postman-collection.png "Kolekce post pro Caselaw ukázku")
   

## <a name="create-an-index"></a>Vytvoření indexu
    
První požadavek používá [rozhraní API Create index](https://docs.microsoft.com/rest/api/searchservice/create-data-source)a vytváří index Azure Search, který ukládá všechna hledaná data. Index určuje všechna pole, parametry a atributy.

Nepotřebujete nutně index pro dolování znalostí, ale indexer nebude možné spustit, dokud není poskytnutý index. 

1. V adrese URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexes?api-version=2019-05-06-Preview`nahraďte `YOUR-AZURE-SEARCH-SERVICE-NAME` názvem vaší vyhledávací služby. 

1. V části záhlaví nahraďte `<YOUR AZURE SEARCH ADMIN API-KEY>` klíč rozhraní API pro správu Azure Search.

1. V části tělo je dokument JSON schématem indexu. Sbalení pro viditelnost, vnější prostředí indexu se skládá z následujících prvků. Kolekce polí odpovídá polím v sadě dat caselaw.

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

1. `fields` Rozbalte kolekci. Obsahuje hromadnou definici indexu skládající se z jednoduchých polí, [komplexních polí](search-howto-complex-data-types.md) s vnořenými podstrukturami a kolekcemi.

   Chvíli počkejte, než zkontrolujete definici pole pro `casebody` komplexní pole na řádcích 302-384. Všimněte si, že komplexní pole může obsahovat další složitá pole, pokud jsou potřeba hierarchické reprezentace. Hierarchické struktury lze modelovat v indexu, jak je znázorněno zde, a také jako projekce v dovednosti, takže se vytvoří vnořená datová struktura ve znalostní bázi Store.

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

1. Kliknutím na **Odeslat** žádost spusťte.  Měl by se zobrazit **stav: 201 byla** vytvořena zpráva jako odpověď.

<a name="create-data-source"></a>

## <a name="create-a-data-source"></a>Vytvoření zdroje dat

Druhý požadavek používá [rozhraní API pro vytvoření zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) pro připojení k úložišti objektů BLOB v Azure. 

1. V adrese URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/datasources?api-version=2019-05-06-Preview`nahraďte `YOUR-AZURE-SEARCH-SERVICE-NAME` názvem vaší vyhledávací služby. 

1. V části záhlaví nahraďte `<YOUR AZURE SEARCH ADMIN API-KEY>` klíč rozhraní API pro správu Azure Search.

1. V části tělo dokument JSON zahrnuje připojovací řetězec vašeho účtu úložiště a název kontejneru objektů BLOB. Připojovací řetězec najdete v Azure Portal v rámci **přístupových klíčů**účtu úložiště. 

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

1. Kliknutím na **Odeslat** žádost spusťte.  Měl by se zobrazit **stav: 201 byla** vytvořena zpráva jako odpověď.



<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Vytvoření úložiště dovednosti a znalostní báze

Třetí požadavek používá [rozhraní API Create dovednosti](https://docs.microsoft.com/rest/api/searchservice/create-skillset)a vytváří objekt Azure Search, který určuje, jaké dovednosti při rozpoznávání se mají volat, jak zřetězit dovednosti společně a nejdůležitější pro tento návod – jak zadat znalostní bázi.

1. V adrese URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/skillsets?api-version=2019-05-06-Preview`nahraďte `YOUR-AZURE-SEARCH-SERVICE-NAME` názvem vaší vyhledávací služby. 

1. V části záhlaví nahraďte `<YOUR AZURE SEARCH ADMIN API-KEY>` klíč rozhraní API pro správu Azure Search.

1. V části tělo je dokument JSON definicí dovednosti. V rámci viditelnosti je vnější prostředí dovednosti tvořeno následujícími prvky. Kolekce definuje rozšíření v paměti, `knowledgeStore` ale definice určuje, jak je výstup uložen. `skills` `cognitiveServices` Definice je vaše připojení k modulům pro obohacení AI.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

1. Rozbalte `cognitiveServices` položku `knowledgeStore` a, aby bylo možné zadat informace o připojení. V tomto příkladu jsou tyto řetězce umístěny za definicí dovednosti do konce textu žádosti. 

   Pro `cognitiveServices`můžete zřídit prostředek na úrovni S0, který je umístěný ve stejné oblasti jako Azure Search. Můžete získat název a klíč Cognitiveservices Account ze stejné stránky v Azure Portal. 
   
   Pro `knowledgeStore`můžete použít stejný připojovací řetězec, který se používá pro kontejner objektů BLOB caselaw.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

1. Rozšiřte shromažďování dovedností, konkrétně Shaperch dovedností na řádcích 85 a 179, v uvedeném pořadí. Dovednost Shaper je důležitá, protože sestavuje datové struktury, které chcete pro dolování znalostí. Během provádění dovednosti jsou tyto struktury jenom v paměti, ale při přechodu k dalšímu kroku uvidíte, jak se tento výstup dá Uložit do znalostní báze pro další zkoumání.

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

1. Rozbalí `projections` element `knowledgeStore`v, počínaje řádkem 262. Projekce určují složení znalostní báze Store. Projekce se zadává v sestavách tabulky – objekty, aktuálně jenom v jednom okamžiku. Jak vidíte v první projekci, je určena, `tables` ale `objects` není. Ve druhém se jedná o opak.

   V Azure Storage se tabulky vytvoří v úložišti tabulek pro každou tabulku, kterou vytvoříte, a každý objekt Získá kontejner ve službě BLOB Storage.

   Objekty blob obvykle obsahují úplný výraz rozšíření. Tabulky typicky obsahují částečná rozšíření v kombinacich, které můžete uspořádat pro konkrétní účely. Tento příklad ukazuje tabulku případů a tabulku stanovisek, ale nezobrazuje se o dalších tabulkách, jako jsou entity, právní zástupce, soudce a strany.

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

1. Kliknutím na **Odeslat** žádost spusťte. Odpověď by měla být **201** a měla by vypadat podobně jako v následujícím příkladu, který ukazuje první část odpovědi.

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

Čtvrtá žádost používá [rozhraní API vytvořit indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)a vytváří indexer Azure Search. Indexer je spouštěcí modul kanálu indexování. Všechny definice, které jste doposud vytvořili, jsou v tomto kroku převedeny do chodu.

1. V adrese URL `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexers?api-version=2019-05-06-Preview`nahraďte `YOUR-AZURE-SEARCH-SERVICE-NAME` názvem vaší vyhledávací služby. 

1. V části záhlaví nahraďte `<YOUR AZURE SEARCH ADMIN API-KEY>` klíč rozhraní API pro správu Azure Search.

1. V části tělo dokument JSON Určuje název indexeru. Indexer vyžaduje zdroj dat a index. Dovednosti je volitelná pro indexer, ale vyžaduje se pro obohacení AI.

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

1. Rozbalte outputFieldMappings. Na rozdíl od fieldMappings, které se používají pro vlastní mapování mezi poli ve zdroji dat a polích v indexu, se outputFieldMappings používá pro mapování obohacených polí vytvořených a vyplněných kanálem na výstupní pole v indexu nebo projekci.

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

1. Kliknutím na **Odeslat** žádost spusťte. Odpověď by měla být **201** a text odpovědi by měl vypadat téměř stejně jako datová část požadavku, kterou jste zadali (oříznuté pro zkrácení).

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

## <a name="explore-knowledge-store"></a>Prozkoumat znalostní bázi Knowledge Store

Můžete začít prozkoumávat hned po importu prvního dokumentu. Pro tuto úlohu použijte [**Průzkumník služby Storage**](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer) na portálu.

Je důležité si uvědomit, že je úložiště znalostí zcela odpojeno od Azure Search. Azure Search index a úložiště znalostní báze obsahují jak datovou reprezentaci, tak obsah, ale z těchto částí. Použijte index pro fulltextové vyhledávání, filtrované hledání a všechny scénáře podporované v Azure Search. Nebo můžete přesunout vpřed se stejným úložištěm znalostní báze a připojit další nástroje pro analýzu obsahu.

## <a name="takeaways"></a>Shrnutí

Nyní jste vytvořili první znalostní bázi Knowledge Store ve službě Azure Storage a použili Průzkumník služby Storage k zobrazení rozšíření. Toto je základní prostředí pro práci s uloženými rozšířeními. 

## <a name="next-steps"></a>Další kroky

Shaper dovednost provádí těžkou zvedání vytváření podrobných datových formulářů, které lze kombinovat do nových tvarů. V dalším kroku si Projděte referenční stránku pro tuto dovednost, kde najdete podrobnosti o tom, jak se používá.

> [!div class="nextstepaction"]
> [Shaper dovednosti – referenční informace](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->
