---
title: 'Kurz: REST a AI přes objekty BLOB Azure'
titleSuffix: Azure Cognitive Search
description: Krokovat příklad extrakce textu a zpracování přirozeného jazyka přes obsah v úložišti objektů Blob pomocí Postman a Azure Cognitive Search REST API.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 8acafa14afab507b704806056efac0f877a47684
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78190718"
---
# <a name="tutorial-use-rest-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Kurz: Ke generování prohledávatelného obsahu z objektů BLOB Azure použijte REST a AI

Pokud máte nestrukturovaný text nebo image v úložišti objektů Blob Azure, [kanál obohacení AI](cognitive-search-concept-intro.md) můžete extrahovat informace a vytvořit nový obsah, který je užitečný pro fulltextové vyhledávání nebo scénáře dolování znalostí. Přestože kanál může zpracovávat bitové kopie, tento kurz REST se zaměřuje na text, použití detekce jazyka a zpracování přirozeného jazyka k vytvoření nových polí, která můžete využít v dotazech, omezujících okolností a filtrech.

Tento kurz používá Postman a [hledání REST API](https://docs.microsoft.com/rest/api/searchservice/) provádět následující úkoly:

> [!div class="checklist"]
> * Začněte s celými dokumenty (nestrukturovaným textem), jako jsou PDF, HTML, DOCX a PPTX v úložišti objektů Blob Azure.
> * Definujte kanál, který extrahuje text, detekuje jazyk, rozpozná entity a detekuje klíčové fráze.
> * Definujte index pro uložení výstupu (nezpracovaný obsah plus dvojice název-hodnota generovaný kanálem).
> * Spusťte kanál ke spuštění transformace a analýzy a vytvořit a načíst index.
> * Prozkoumejte výsledky pomocí fulltextového vyhledávání a bohaté syntaxe dotazu.

Pokud nemáte předplatné Azure, otevřete si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Desktopová aplikace Postman](https://www.getpostman.com/)
+ [Vytvoření](search-create-service-portal.md) nebo [vyhledání existující vyhledávací služby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Můžete použít bezplatnou službu pro tento kurz. Bezplatná vyhledávací služba vás omezí na tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Než začnete, ujistěte se, že máte ve službě prostor pro přijetí nových zdrojů.

## <a name="download-files"></a>Stažení souborů

1. Otevřete tuto [složku OneDrivu](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) a v levém horním rohu klikněte na **Stáhnout** a zkopírujte soubory do počítače. 

1. Klepněte pravým tlačítkem myši na soubor zip a vyberte **možnost Extrahovat vše**. K dispozici je 14 souborů různých typů. Budete používat 7 pro toto cvičení.

## <a name="1---create-services"></a>1 - Vytváření služeb

Tento kurz používá Azure Cognitive Search pro indexování a dotazy, Cognitive Services na back-endu pro obohacení AI a úložiště objektů blob Azure k poskytování dat. Tento kurz zůstane pod bezplatným přidělením 20 transakcí na indexer za den ve službách Cognitive Services, takže jediné služby, které potřebujete vytvořit, jsou vyhledávání a ukládání.

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

1. Klepnutím na **tlačítko + Kontejner** vytvořte kontejner a pojmenujte jej *cog-search-demo*.

1. Vyberte *cog-search-demo* a kliknutím na **Nahrát** otevřete složku, do které jste uložili soubory pro stahování. Vyberte všechny soubory, které nejsou obrazové. Měli byste mít 7 souborů. Chcete-li nahrát, klepněte na tlačítko **OK.**

   ![Nahrání ukázkových souborů](media/cognitive-search-tutorial-blob/sample-files.png "Nahrání ukázkových souborů")

1. Než opustíte Azure Storage, získejte připojovací řetězec, abyste mohli formulovat připojení v Azure Cognitive Search. 

   1. Přejděte zpět na stránku Přehled vašeho účtu úložiště (jako příklad jsme použili *blobstragewestus).* 
   
   1. V levém navigačním podokně vyberte **přístupové klávesy** a zkopírujte jeden z připojovacích řetězců. 

   Připojovací řetězec je adresa URL podobná následujícímu příkladu:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Uložte připojovací řetězec do programu Poznámkový blok. Budete ji potřebovat později při nastavování připojení ke zdroji dat.

### <a name="cognitive-services"></a>Cognitive Services

Obohacení umělou a ii je podpořeno službami Cognitive Services, včetně analýzy textu a počítačového vidění pro přirozené zpracování jazyka a obrazu. Pokud vaším cílem bylo dokončit skutečný prototyp nebo projekt, by v tomto bodě poskytování cognitive services (ve stejné oblasti jako Azure Cognitive Search), takže můžete připojit k operacím indexování.

Pro toto cvičení však můžete přeskočit zřizování prostředků, protože Azure Cognitive Search můžete připojit k cognitive services na pozadí a poskytnout 20 volných transakcí na spuštění indexeru. Vzhledem k tomu, že tento kurz používá 7 transakcí, je povolená povolenka dostatečná. Pro větší projekty plánujte na zřizování služeb Cognitive Services na úrovni S0 s průběžným platbami. Další informace naleznete v [tématu Attach Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Třetí komponentou je Azure Cognitive Search, kterou můžete [vytvořit na portálu](search-create-service-portal.md). K dokončení tohoto návodu můžete použít úroveň Free. 

Stejně jako u úložiště objektů Blob Azure, věnujte chvilku shromažďování přístupového klíče. Dále, když začnete strukturovat požadavky, budete muset poskytnout koncový bod a klíč rozhraní api správce, který se používá k ověření každého požadavku.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Získání klíče api správce a adresy URL pro Azure Cognitive Search

1. [Přihlaste se na portál Azure](https://portal.azure.com/)Portal a na stránce **Přehled** vyhledávací služby získáte název vyhledávací služby. Název služby můžete potvrdit kontrolou adresy URL koncového bodu. Pokud by adresa `https://mydemo.search.windows.net`URL koncového bodu `mydemo`byla , byl by název služby .

2. V **nastavení** > **klíče**, získat klíč správce pro úplná práva ke službě. Existují dva zaměnitelné klíče pro správu, které jsou k dispozici pro kontinuitu podnikání v případě, že potřebujete převrátit jeden. Primární nebo sekundární klíč můžete použít při požadavcích na přidávání, úpravy a odstranění objektů.

   Získejte také klíč dotazu. Je osvědčeným postupem pro vydávání požadavků na dotazy s přístupem jen pro čtení.

   ![Získání názvu služby a klíčů pro správce a dotazy](media/search-get-started-nodejs/service-name-and-keys.png)

Všechny požadavky vyžadují klíč rozhraní api v záhlaví každé žádosti odeslané do vaší služby. Platný klíč vytvoří vztah důvěryhodnosti na základě požadavku mezi aplikací odesílající požadavek a službou, která ji zpracovává.

## <a name="2---set-up-postman"></a>2 - Nastavit pošťáka

Spusťte Postman a nastavte požadavek HTTP. Pokud nejste obeznámeni s tímto nástrojem, najdete [v tématu prozkoumat Azure Cognitive Search REST API pomocí Postman](search-get-started-postman.md).

Metody požadavku použité v tomto kurzu jsou **POST**, **PUT**a **GET**. Tyto metody použijete ke čtyřem voláním rozhraní API do vyhledávací služby: vytvořte zdroj dat, sadu dovedností, index a indexer.

V záhlaví nastavte "Typ obsahu" `application/json` a `api-key` nastavte na klíč rozhraní api správce vaší služby Azure Cognitive Search. Jakmile nastavíte záhlaví, můžete je použít pro každý požadavek v tomto cvičení.

  ![Adresa URL a záhlaví požadavku pošťáka](media/search-get-started-postman/postman-url.png "Adresa URL a záhlaví požadavku pošťáka")

## <a name="3---create-the-pipeline"></a>3 - Vytvoření potrubí

V Azure Cognitive Search, Zpracování AI dochází během indexování (nebo přilnutí dat). Tato část návodu vytvoří čtyři objekty: zdroj dat, definice indexu, skillset, indexer. 

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

[Objekt zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) poskytuje připojovací řetězec ke kontejneru objektů Blob obsahujícímu soubory.

1. Použijte **POST** a následující adresu URL a nahraďte název SLUŽBY skutečným názvem služby.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2019-05-06
   ```

1. V požadavku **Tělo**, zkopírujte následující definici `connectionString` JSON, která nahradí skutečné připojení účtu úložiště. 

   Nezapomeňte také upravit název kontejneru. Navrhli jsme "cog-search-demo" pro název kontejneru v předchozím kroku.

    ```json
    {
      "name" : "cog-search-demo-ds",
      "description" : "Demo files to demonstrate cognitive search capabilities.",
      "type" : "azureblob",
      "credentials" :
      { "connectionString" :
        "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-ACCOUNT-KEY>;"
      },
      "container" : { "name" : "<YOUR-BLOB-CONTAINER-NAME>" }
    }
    ```
1. Odešlete požadavek. Měli byste vidět stavový kód 201 potvrzující úspěch. 

Pokud dostanete chybu 403 nebo 404, zkontrolujte vytvoření požadavku: v koncovém bodu by mělo být `api-version=2019-05-06`, v hlavičce za `Content-Type` by mělo být `api-key` a jeho hodnota musí být pro vyhledávací službu platná. Můžete chtít spustit dokument JSON prostřednictvím online validátoru JSON, abyste se ujistili, že syntaxe je správná. 

### <a name="step-2-create-a-skillset"></a>Krok 2: Vytvoření sady dovedností

[Objekt skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) je sada kroků obohacení aplikovaných na váš obsah. 

1. Použijte **PUT** a následující url, nahradí vaše-SERVICE-NAME se skutečným názvem služby.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-ss?api-version=2019-05-06
    ```

1. V požadavku **Tělo**, zkopírujte definici JSON níže. Tato sada dovedností se skládá z následujících vestavěných dovedností.

   | Dovednost                 | Popis    |
   |-----------------------|----------------|
   | [Uznání entity](cognitive-search-skill-entity-recognition.md) | Extrahuje názvy osob, organizací a umístění z obsahu v kontejneru objektů blob. |
   | [Rozpoznávání jazyka](cognitive-search-skill-language-detection.md) | Detekuje jazyk obsahu. |
   | [Rozdělení textu](cognitive-search-skill-textsplit.md)  | Rozdělí velký obsah na menší bloky před voláním klíčové fráze extrakce dovednost. Extrakce klíčových frází přijímá vstup složený z 50 000 znaků nebo méně. Některé ze zdrojových souborů je nutné rozdělit, aby se do tohoto limitu vešly. |
   | [Extrakce klíčových frází](cognitive-search-skill-keyphrases.md) | Vytáhne hlavní klíčové fráze. |

   Pro obsah dokumentu se využijí jednotlivé dovednosti. Během zpracování Azure Cognitive Search rozlouskne každý dokument číst obsah z různých formátů souborů. Nalezený text, který pochází ze zdrojového souboru, se umístí do vygenerovaného pole ```content```, jednoho pro každý dokument. Jako takový vstup ```"/document/content"```se stane .

   Pro extrakci klíčových frází, protože používáme dovednost rozdělovače textu k rozdělení ```"document/pages/*"``` větších souborů na stránky, ```"/document/content"```je kontext pro dovednost extrakce klíčových frází (pro každou stránku v dokumentu) namísto .

    ```json
    {
      "description": "Extract entities, detect language and extract key-phrases",
      "skills":
      [
        {
          "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
          "categories": [ "Person", "Organization", "Location" ],
          "defaultLanguageCode": "en",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "persons", "targetName": "persons" },
            { "name": "organizations", "targetName": "organizations" },
            { "name": "locations", "targetName": "locations" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "languageCode", "targetName": "languageCode" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
          "textSplitMode" : "pages",
          "maximumPageLength": 4000,
          "inputs": [
            { "name": "text", "source": "/document/content" },
            { "name": "languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "textItems", "targetName": "pages" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
          "context": "/document/pages/*",
          "inputs": [
            { "name": "text", "source": "/document/pages/*" },
            { "name":"languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "keyPhrases", "targetName": "keyPhrases" }
          ]
        }
      ]
    }
    ```
    Grafickou reprezentaci sady dovedností najdete níže. 

    ![Pochopte sadu dovedností](media/cognitive-search-tutorial-blob/skillset.png "Pochopte sadu dovedností")

1. Odešlete požadavek. Pošťák by měl vrátit stavový kód 201 potvrzující úspěch. 

> [!NOTE]
> Výstupy se dají namapovat na index, použít jako vstup do podřízené dovednosti, nebo využít oběma způsoby tak, jak se to dělá s kódem jazyka. V indexu je kód jazyka užitečný při filtrování. Jako vstup se kód jazyka používá v dovednostech analýzy textu, čímž se jazykovým pravidlům poskytne informace o dělení slov. Další informace o základních principech sady dovedností najdete v článku o [definování sady dovedností](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Krok 3: Vytvoření indexu

[Index](https://docs.microsoft.com/rest/api/searchservice/create-index) poskytuje schéma použité k vytvoření fyzického vyjádření obsahu v invertovaných indexech a dalších konstrukcích v Azure Cognitive Search. Největší součástí indexu je kolekce polí, kde datový typ a atributy určují obsah a chování v Azure Cognitive Search.

1. Použijte **PUT** a následující url, nahradí vaše-SERVICE-NAME se skutečným názvem služby, aby název indexu.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2019-05-06
   ```

1. V požadavku **Tělo**zkopírujte následující definici JSON. Pole `content` ukládá samotný dokument. Další pole `languageCode` `keyPhrases`pro `organizations` , a představují nové informace (pole a hodnoty) vytvořené skillset.

    ```json
    {
      "fields": [
        {
          "name": "id",
          "type": "Edm.String",
          "key": true,
          "searchable": true,
          "filterable": false,
          "facetable": false,
          "sortable": true
        },
        {
          "name": "metadata_storage_name",
          "type": "Edm.String",
          "searchable": false,
          "filterable": false,
          "facetable": false,
          "sortable": false
        },
        {
          "name": "content",
          "type": "Edm.String",
          "sortable": false,
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "languageCode",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "keyPhrases",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "persons",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "organizations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "locations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        }
      ]
    }
    ```

1. Odešlete požadavek. Pošťák by měl vrátit stavový kód 201 potvrzující úspěch. 

### <a name="step-4-create-and-run-an-indexer"></a>Krok 4: Vytvoření a spuštění indexeru

[Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) řídí potrubí. Tři komponenty, které jste dosud vytvořili (zdroj dat, skillset, index) jsou vstupy do indexeru. Vytvoření indexeru v Azure Cognitive Search je událost, která uvádí celý kanál do pohybu. 

1. Použijte **PUT** a následující url, nahradí vaše-SERVICE-NAME s aktuální název služby, aby název indexeru.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
   ```

1. V požadavku **Tělo**, zkopírujte definici JSON níže. Všimněte si prvků mapování polí; tato mapování jsou důležitá, protože definují tok dat. 

   Jsou `fieldMappings` zpracovány před skillset, odesílání obsahu ze zdroje dat do cílových polí v indexu. Mapování polí použijete k odeslání existujícího nezměněného obsahu do indexu. Pokud jsou názvy a typy polí na obou koncích stejné, není vyžadováno žádné mapování.

   Jsou `outputFieldMappings` pro pole vytvořená dovednostmi, a proto zpracována po spuštění sady dovedností. Odkazy `sourceFieldNames` na in `outputFieldMappings` neexistují, dokud je nevytvoří popraskání dokumentu nebo obohacení. Toto `targetFieldName` je pole v indexu definované ve schématu indexu.

    ```json
    {
      "name":"cog-search-demo-idxr",    
      "dataSourceName" : "cog-search-demo-ds",
      "targetIndexName" : "cog-search-demo-idx",
      "skillsetName" : "cog-search-demo-ss",
      "fieldMappings" : [
        {
          "sourceFieldName" : "metadata_storage_path",
          "targetFieldName" : "id",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "metadata_storage_name",
          "targetFieldName" : "metadata_storage_name",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "content",
          "targetFieldName" : "content"
        }
      ],
      "outputFieldMappings" :
      [
        {
          "sourceFieldName" : "/document/persons",
          "targetFieldName" : "persons"
        },
        {
          "sourceFieldName" : "/document/organizations",
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/locations",
          "targetFieldName" : "locations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*",
          "targetFieldName" : "keyPhrases"
        },
        {
          "sourceFieldName": "/document/languageCode",
          "targetFieldName": "languageCode"
        }
      ],
      "parameters":
      {
        "maxFailedItems":-1,
        "maxFailedItemsPerBatch":-1,
        "configuration":
        {
          "dataToExtract": "contentAndMetadata",
          "parsingMode": "default",
          "firstLineContainsHeaders": false,
          "delimitedTextDelimiter": ","
        }
      }
    }
    ```

1. Odešlete požadavek. Pošťák by měl vrátit stavový kód 201 potvrzující úspěšné zpracování. 

   Buďte připravení na to, že může trvat i několik minut, než se tento krok dokončí. I když je sada dat malá, analytické dovednosti jsou výpočetně náročné. 

> [!NOTE]
> Vytvoření indexeru vyvolá kanál. Pokud dojde k nějakému problému při komunikaci s daty, při mapování vstupů a výstupů nebo s pořadím operací, zobrazí se v této fázi. Pokud chcete kanál spustit znovu s pozměněným kódem nebo skriptem, bude možná nutné nejdříve zahodit objekty. Další informace najdete v článku o [resetování a opětovném spuštění](#reset).

#### <a name="about-indexer-parameters"></a>Parametry indexeru

Skript nastaví ```"maxFailedItems"``` na -1, což dá modulu indexování pokyn, aby během importování dat ignoroval chyby. To je přijatelné, protože ve zdroji demo dat je tak málo dokumentů. Pro větší zdroje dat by tato hodnota byla větší než 0.

Příkaz ```"dataToExtract":"contentAndMetadata"``` říká indexeru automaticky extrahovat obsah z různých formátů souborů, stejně jako metadata vztahující se ke každému souboru. 

Když se extrahuje obsah, můžete nastavit ```imageAction```, aby se z obrázků nalezených ve zdroji dat extrahoval text. Konfigurace ```"imageAction":"generateNormalizedImages"``` v kombinaci s dovedností OCR a dovedností sloučení textu říká indexeru, aby extrahoval text z obrázků (například slovo "stop" ze znaménko stop provozu) a vkládal jej jako součást pole obsahu. Toto chování platí jak pro obrázky vložené do dokumentů (třeba obrázek v souboru PDF), tak pro obrázky nalezené ve zdroji dat, např. soubor JPG.

## <a name="4---monitor-indexing"></a>4 - Sledování indexování

Indexování a obohacení začít, jakmile odešlete vytvořit indexer žádost. V závislosti na tom, které kognitivní dovednosti, které jste definovali, může indexování chvíli trvat. Pokud chcete zjistit, jestli indexer pořád běží, pošlete následující požadavek, který zjistí jeho stav.

1. Použijte **GET** a následující adresu URL, která nahradí název SLUŽBY skutečným názvem služby, abyste pojmenovali indexer.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2019-05-06
   ```

1. Zkontrolujte odpověď zjistit, zda je spuštěn indexer, nebo zobrazit informace o chybě a upozornění.  

Pokud používáte free vrstvu, očekává se následující zpráva: "Nelze extrahovat obsah nebo metadata z dokumentu. Zkrácený extrahovaný text na znaky '32768'. Tato zpráva se zobrazí, protože indexování objektů blob na úrovni Free má[limit 32 kB na extrakci znaků](search-limits-quotas-capacity.md#indexer-limits). Tato zpráva se pro tuto sadu dat na vyšších úrovních nezobrazí. 

> [!NOTE]
> Upozornění jsou běžné v některých scénářích a ne vždy označují problém. Pokud například kontejner objektů blob obsahuje soubory bitové kopie a kanál nezpracovává obrázky, zobrazí se upozornění, že obrázky nebyly zpracovány.

## <a name="5---search"></a>5 - Hledání

Teď, když jste vytvořili nová pole a informace, spusťme některé dotazy, abychom pochopili hodnotu kognitivního vyhledávání, protože se vztahuje k typickému scénáři vyhledávání.

Připomeňme, že jsme začali s obsahem objektů `content` blob, kde je celý dokument zabalen do jednoho pole. Toto pole můžete vyhledat a najít shody s dotazy.

1. Pomocí **příkazu GET** a následující adresy URL, která nahradí název SLUŽBY skutečným názvem služby, můžete vyhledat `content` instance výrazu nebo fráze, vrátit pole a počet odpovídajících dokladů.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?search=*&$count=true&$select=content?api-version=2019-05-06
   ```
   
   Výsledky tohoto dotazu vrátit obsah dokumentu, což je stejný výsledek, který by získal, pokud používá indexer objektů blob bez kanálu kognitivní vyhledávání. Toto pole lze prohledávat, ale neproveditelné, pokud chcete použít omezující podmínky, filtry nebo automatické dokončování.

   ![Výstup pole obsahu](media/cognitive-search-tutorial-blob/content-output.png "Výstup pole obsahu")
   
1. Pro druhý dotaz vraťte některá nová pole vytvořená kanálem (osoby, organizace, umístění, languageCode). Jsme vynechání keyPhrases pro stručnost, ale měli byste zahrnout, pokud chcete vidět tyto hodnoty.

   ```http
   https://mydemo.search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2019-05-06
   ```
   Pole v příkazu $select obsahují nové informace vytvořené z možností zpracování přirozeného jazyka služby Cognitive Services. Jak můžete očekávat, ve výsledcích a změnách mezi dokumenty je určitý šum, ale v mnoha případech analytické modely vytvářejí přesné výsledky.

   Následující obrázek ukazuje výsledky otevřeného dopisu Satya Nadella po převzetí role generálního ředitele společnosti Microsoft.

   ![Výstup potrubí](media/cognitive-search-tutorial-blob/pipeline-output.png "Výstup potrubí")

1. Chcete-li zjistit, jak můžete tato pole využít, přidejte parametr omezující_ doplněk, který vrátí agregaci odpovídajících dokumentů podle umístění.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2019-05-06
   ``` 

   V tomto příkladu jsou pro každé umístění 2 nebo 3 shody.

   ![Výstup omezující fazety](media/cognitive-search-tutorial-blob/facet-output.png "Výstup omezující fazety")
   

1. V tomto posledním příkladu použijte filtr na kolekci organizací a vraťte dvě shody pro kritéria filtru založená na nasdaq.

   ```http
   cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2019-05-06
   ```

Tyto dotazy ilustrují několik způsobů, jak můžete pracovat se syntaxí dotazu a filtry na nová pole vytvořená kognitivním vyhledáváním. Další příklady dotazů naleznete [v tématu Příklady v rozhraní REST API pro hledání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples), v [ukázkách dotazu syntaxe a](search-query-simple-examples.md)v [příkladech dotazů Full Lucene](search-query-lucene-examples.md).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

V raných experimentálních fázích vývoje je nejpraktičtějším přístupem pro iteraci návrhu odstranění objektů z Azure Cognitive Search a povolení jejich opětovného sestavení kódu. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Portál můžete použít k odstranění indexů, indexerů, zdrojů dat a skillsets. Při odstranění indexeru můžete volitelně selektivně odstranit index, skillset a zdroj dat současně.

![Odstranění vyhledávacích objektů](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Odstranění vyhledávacích objektů na portálu")

Nebo použijte **DELETE** a poskytněte adresy URL každému objektu. Následující příkaz odstraní indexer.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
```

Při úspěšném odstranění se vrátí kód stavu 204.

## <a name="takeaways"></a>Shrnutí

Tento kurz ukazuje základní postup sestavení kanálu pro rozšířené indexování, při kterém se vytvářejí součásti: zdroj dat, sada dovedností, index a indexer.

Byly zavedeny [vestavěné dovednosti](cognitive-search-predefined-skills.md) spolu s definicí dovedností a mechanikou řetězení dovedností prostřednictvím vstupů a výstupů. Také jste `outputFieldMappings` zjistili, že v definici indexeru je vyžadovánpro směrování obohacené hodnoty z kanálu do indexu prohledávatelné na Azure Cognitive Search služby.

Nakonec jste se dozvěděli, jak testovat výsledky a resetovat systém pro další iterace. Zjistili jste, že zasílání dotazů na index vrací výstup vytvořený kanálem rozšířeného indexování. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Při práci ve vlastním předplatném je na konci projektu vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu Všechny prostředky nebo skupiny prostředků v levém navigačním podokně.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni se všemi objekty v kanálu obohacení AI, podívejme se blíže na definice dovedností a individuální dovednosti.

> [!div class="nextstepaction"]
> [Jak vytvořit sadu dovedností](cognitive-search-defining-skillset.md)