---
title: 'Kurz: REST a AI přes objekty blob Azure'
titleSuffix: Azure Cognitive Search
description: Projděte si příklad extrakce textu a zpracování přirozeného jazyka přes obsah v úložišti objektů BLOB pomocí doplňku a rozhraní REST API služby Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 7db2d89c112c5f874460f5e6955cdce90cc2f9ae
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "78162995"
---
# <a name="tutorial-use-rest-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Kurz: použití REST a AI k vygenerování prohledávatelných obsahu z objektů blob Azure

Pokud máte v úložišti objektů BLOB v Azure nestrukturovaný text nebo obrázky, [kanál pro rozšíření AI](cognitive-search-concept-intro.md) může extrahovat informace a vytvořit nový obsah, který je vhodný pro scénáře fulltextového vyhledávání nebo dolování ve znalostní bázi. I když může kanál zpracovat image, tento kurz se zaměřuje na text, používá se pro detekci jazyka a zpracování přirozeného jazyka a vytváří nová pole, která můžete využít v dotazech, omezujících objektech a filtrech.

V tomto kurzu použijete příkaz post a [REST](https://docs.microsoft.com/rest/api/searchservice/) k provedení následujících úkolů:

> [!div class="checklist"]
> * Začněte s celými dokumenty (nestrukturovaný text), například PDF, HTML, DOCX a PPTX v úložišti objektů BLOB v Azure.
> * Definujte kanál, který extrahuje text, detekuje jazyk, rozpoznává entity a detekuje klíčové fráze.
> * Definujte index pro uložení výstupu (nezpracovaný obsah, plus páry název-hodnota vygenerované kanály).
> * Spusťte kanál, abyste mohli začít transformovat a analyzovat a vytvořit a načíst index.
> * Prozkoumejte výsledky pomocí fulltextového vyhledávání a bohatou syntaxí dotazu.

Pokud ještě nemáte předplatné Azure, otevřete si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Desktopová aplikace Postman](https://www.getpostman.com/)
+ [Vytvoření](search-create-service-portal.md) nebo [vyhledání existující vyhledávací služby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Pro tento kurz můžete použít bezplatnou službu. Bezplatná vyhledávací služba omezuje tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Než začnete, ujistěte se, že máte ve své službě místo pro přijímání nových prostředků.

## <a name="download-files"></a>Stažení souborů

1. Otevřete tuto [složku OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) a v levém horním rohu klikněte na **Stáhnout** a zkopírujte soubory do počítače. 

1. Klikněte pravým tlačítkem na soubor zip a vyberte **Extrahovat vše**. Existuje 14 souborů různých typů. Pro toto cvičení budete používat 7.

## <a name="1---create-services"></a>1\. vytvoření služeb

V tomto kurzu se používá Azure Kognitivní hledání pro indexování a dotazy, Cognitive Services v back-endu pro rozšíření AI a Azure Blob Storage k poskytnutí dat. Tento kurz zůstává v rámci bezplatného přidělení 20 transakcí na indexer za den v Cognitive Services, takže je potřeba, abyste vytvořili jenom služby pro vyhledávání a úložiště.

Pokud je to možné, vytvořte oba ve stejné oblasti a skupině prostředků pro možnost blízkost a spravovatelnost. V praxi může být váš účet Azure Storage v jakékoli oblasti.

### <a name="start-with-azure-storage"></a>Začínáme s Azure Storage

1. [Přihlaste se k Azure Portal](https://portal.azure.com/) a klikněte na **+ vytvořit prostředek**.

1. Vyhledejte *účet úložiště* a vyberte nabídku účtu úložiště od Microsoftu.

   ![Vytvořit účet úložiště](media/cognitive-search-tutorial-blob/storage-account.png "Vytvořit účet úložiště")

1. Na kartě základy jsou vyžadovány následující položky. Přijměte výchozí hodnoty pro všechno ostatní.

   + **Skupina prostředků**. Vyberte existující jednu nebo vytvořte novou, ale použijte stejnou skupinu pro všechny služby, abyste je mohli souhrnně spravovat.

   + **Název účtu úložiště** Pokud se domníváte, že máte více prostředků stejného typu, použijte název k jednoznačnému odstranění podle typu a oblasti, například *blobstoragewestus*. 

   + **Umístění**. Pokud je to možné, vyberte stejné umístění, které se používá pro Azure Kognitivní hledání a Cognitive Services. Jediné místo má za vyrušení poplatky za šířku pásma.

   + **Druh účtu**. Vyberte výchozí *StorageV2 (obecné účely v2)* .

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** službu vytvořte.

1. Po vytvoření klikněte na **Přejít k prostředku** a otevřete stránku Přehled.

1. Klikněte na služba **BLOB** Service.

1. Kliknutím na **+ kontejner** vytvořte kontejner a pojmenujte ho *ozubeného kola-Search-demo*.

1. Vyberte *ozubeného kola-Search-demo* a potom kliknutím na **Odeslat** otevřete složku, kam jste uložili soubory ke stažení. Vyberte všechny soubory bez imagí. Měli byste mít 7 souborů. Kliknutím na tlačítko **OK** nahrajte.

   ![Nahrání ukázkových souborů](media/cognitive-search-tutorial-blob/sample-files.png "Nahrání ukázkových souborů")

1. Než ponecháte Azure Storage, Získejte připojovací řetězec, abyste mohli formulovat připojení v Azure Kognitivní hledání. 

   1. Přejděte zpět na stránku Přehled vašeho účtu úložiště (jako příklad jsme použili *blobstragewestus* ). 
   
   1. V levém navigačním podokně vyberte **přístupové klíče** a zkopírujte jeden z připojovacích řetězců. 

   Připojovací řetězec je adresa URL podobná následujícímu příkladu:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Uložte připojovací řetězec do poznámkového bloku. Budete ho potřebovat později při nastavování připojení ke zdroji dat.

### <a name="cognitive-services"></a>Kognitivní služby

Obohacení AI je zajištěno Cognitive Services, včetně Analýza textu a Počítačové zpracování obrazu pro zpracování přirozeného jazyka a obrazu. Pokud by vaším cílem bylo dokončit skutečný prototyp nebo projekt, měli byste v tomto okamžiku zřídit Cognitive Services (ve stejné oblasti jako Azure Kognitivní hledání), abyste ho mohli připojit k operacím indexování.

Pro toto cvičení ale můžete přeskočit zřizování prostředků, protože Azure Kognitivní hledání se může připojit k Cognitive Services na pozadí a poskytnout vám 20 bezplatných transakcí na indexer. Vzhledem k tomu, že tento kurz používá 7 transakcí, je bezplatné přidělení dostatečné. Pro větší projekty Naplánujte zřizování Cognitive Services na úrovni průběžných plateb. Další informace najdete v tématu věnovaném [připojení Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Třetí součástí je Azure Kognitivní hledání, kterou můžete vytvořit na [portálu](search-create-service-portal.md). K dokončení tohoto Názorného postupu můžete použít bezplatnou úroveň. 

Stejně jako u služby Azure Blob Storage si pro získání přístupového klíče chvíli počkejte. Když při zahájení strukturování požadavků začnete, budete muset zadat koncový bod a klíč rozhraní API pro správu, který se použije k ověření každého požadavku.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Získání klíčového rozhraní API pro správu a adresy URL pro Azure Kognitivní hledání

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte název vaší vyhledávací služby. Název služby můžete potvrdit zkontrolováním adresy URL koncového bodu. Pokud se `https://mydemo.search.windows.net`adresa URL koncového bodu, bude název služby `mydemo`.

2. V části **nastavení** > **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   Získejte taky klíč dotazu. Osvědčeným postupem je vystavovat požadavky na dotazy s přístupem jen pro čtení.

   ![Získání názvu služby a klíčů pro správu a dotazy](media/search-get-started-nodejs/service-name-and-keys.png)

Všechny požadavky vyžadují klíč rozhraní API-Key v hlavičce všech požadavků odeslaných službě. Platný klíč vytváří vztah důvěryhodnosti na základě jednotlivých požadavků mezi aplikací odesílající požadavek a službu, která ho zpracovává.

## <a name="2---set-up-postman"></a>2 – nastavení post

Spusťte Postman a nastavte požadavek HTTP. Pokud tento nástroj neznáte, přečtěte si téma [prozkoumání rozhraní REST API služby Azure kognitivní hledání pomocí služby post](search-get-started-postman.md).

Metody žádosti použité v tomto kurzu jsou **post**, **Put**a **Get**. Tyto metody použijete k vytvoření čtyř volání rozhraní API vaší vyhledávací služby: vytvořit zdroj dat, dovednosti, index a indexer.

V části hlavičky nastavte typ Content-Type na `application/json` a nastavte `api-key` na klíč rozhraní API pro správu služby Azure Kognitivní hledání. Po nastavení hlaviček je můžete použít pro každý požadavek v tomto cvičení.

  ![Adresa URL a záhlaví žádosti post](media/search-get-started-postman/postman-url.png "Adresa URL a záhlaví žádosti post")

## <a name="3---create-the-pipeline"></a>3\. vytvoření kanálu

Ve službě Azure Kognitivní hledání se při indexování (nebo ingestování dat) objevuje zpracování AI. Tato část návodu vytvoří čtyři objekty: zdroj dat, index definice, dovednosti, indexer. 

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

[Objekt zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) poskytuje připojovací řetězec k kontejneru objektů blob, který obsahuje soubory.

1. Použijte **post** a následující adresu URL, kde nahraďte název vaší služby skutečným názvem vaší služby.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2019-05-06
   ```

1. Do **textu**žádosti zkopírujte následující definici JSON a nahraďte `connectionString` skutečným připojením k vašemu účtu úložiště. 

   Nezapomeňte také upravit název kontejneru. Navrhl (a) "ozubeného kola-Search-demo" pro název kontejneru v předchozím kroku.

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
1. Odešlete požadavek. Měl by se zobrazit stavový kód 201 potvrzující úspěch. 

Pokud dostanete chybu 403 nebo 404, zkontrolujte vytvoření požadavku: v koncovém bodu by mělo být `api-version=2019-05-06`, v hlavičce za `api-key` by mělo být `Content-Type` a jeho hodnota musí být pro vyhledávací službu platná. Dokument JSON můžete chtít spustit pomocí online validátoru JSON, abyste se ujistili, že je syntaxe správná. 

### <a name="step-2-create-a-skillset"></a>Krok 2: vytvoření dovednosti

[Objekt dovednosti](https://docs.microsoft.com/rest/api/searchservice/create-skillset) je sada kroků rozšíření, které se aplikují na váš obsah. 

1. Použijte příkaz **Put** a následující adresu URL, kde nahraďte název vaší služby skutečným názvem vaší služby.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-ss?api-version=2019-05-06
    ```

1. Do **textu**žádosti ZKOPÍRUJTE definici JSON níže. Tento dovednosti se skládá z následujících integrovaných dovedností.

   | Tuhle                 | Popis    |
   |-----------------------|----------------|
   | [Rozpoznávání entit](cognitive-search-skill-entity-recognition.md) | Extrahuje jména lidí, organizací a umístění z obsahu v kontejneru objektů BLOB. |
   | [Rozpoznávání jazyka](cognitive-search-skill-language-detection.md) | Zjistí jazyk obsahu. |
   | [Rozdělení textu](cognitive-search-skill-textsplit.md)  | Před voláním dovednosti pro extrakci klíčových frází rozdělí velký obsah do menších bloků dat. Extrakce klíčových frází přijímá vstup složený z 50 000 znaků nebo méně. Některé ze zdrojových souborů je nutné rozdělit, aby se do tohoto limitu vešly. |
   | [Extrakce klíčových frází](cognitive-search-skill-keyphrases.md) | Vyžádá si hlavní klíčové fráze. |

   Pro obsah dokumentu se využijí jednotlivé dovednosti. Během zpracování Azure Kognitivní hledání napraskliní každý dokument, aby četl obsah z různých formátů souborů. Nalezený text, který pochází ze zdrojového souboru, se umístí do vygenerovaného pole ```content```, jednoho pro každý dokument. V takovém případě se vstup ```"/document/content"```.

   Pro extrakci klíčových frází, protože používáme oddělení text k rozdělení větších souborů na stránky, kontext pro extrakci klíčových frází je ```"document/pages/*"``` (pro každou stránku v dokumentu) místo ```"/document/content"```.

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

    ![Pochopení dovednosti](media/cognitive-search-tutorial-blob/skillset.png "Pochopení dovednosti")

1. Odešlete požadavek. Metoda post by měla vrátit stavový kód 201 potvrzující úspěch. 

> [!NOTE]
> Výstupy se dají namapovat na index, použít jako vstup do podřízené dovednosti, nebo využít oběma způsoby tak, jak se to dělá s kódem jazyka. V indexu je kód jazyka užitečný při filtrování. Jako vstup se kód jazyka používá v dovednostech analýzy textu, čímž se jazykovým pravidlům poskytne informace o dělení slov. Další informace o základních principech sady dovedností najdete v článku o [definování sady dovedností](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Krok 3: vytvoření indexu

[Index](https://docs.microsoft.com/rest/api/searchservice/create-index) poskytuje schéma používané k vytvoření fyzického výrazu vašeho obsahu ve invertovaných indexech a jiných konstrukcích v Azure kognitivní hledání. Největší součástí indexu je kolekce polí, kde typ dat a atributy určují obsah a chování v Azure Kognitivní hledání.

1. Použijte příkaz **Put** a následující adresu URL, kde nahraďte název vaší služby skutečným názvem vaší služby a pojmenujte index.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2019-05-06
   ```

1. Do **textu**žádosti zkopírujte následující definici JSON. Pole `content` ukládá samotný dokument. Další pole pro `languageCode`, `keyPhrases`a `organizations` reprezentují nové informace (pole a hodnoty) vytvořené v dovednosti.

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

1. Odešlete požadavek. Metoda post by měla vrátit stavový kód 201 potvrzující úspěch. 

### <a name="step-4-create-and-run-an-indexer"></a>Krok 4: vytvoření a spuštění indexeru

[Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) vyřídí kanál. Tři komponenty, které jste doposud vytvořili (zdroj dat, dovednosti, index), jsou vstupy pro indexer. Vytvoření indexeru v Azure Kognitivní hledání je událost, která vloží celý kanál do pohybu. 

1. Použijte příkaz **Put** a následující adresu URL, kde NAHRAĎte název vaší služby skutečným názvem vaší služby a pojmenujte indexer.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
   ```

1. Do **textu**žádosti ZKOPÍRUJTE definici JSON níže. Všimněte si elementů mapování polí; Tato mapování jsou důležitá, protože definují tok dat. 

   `fieldMappings` jsou zpracovány před dovednosti a odesílají obsah ze zdroje dat do cílových polí v indexu. K odeslání existujícího nezměněného obsahu do indexu budete používat mapování polí. Pokud jsou názvy polí a typy na obou koncích stejné, není nutné žádné mapování.

   `outputFieldMappings` jsou pro pole vytvořená dovednostmi, která se následně zpracovala po spuštění dovednosti. Odkazy na `sourceFieldNames` v `outputFieldMappings` neexistují, dokud je nevytvoří dokument trhliny nebo rozšíření. `targetFieldName` je pole v indexu definované ve schématu indexu.

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

1. Odešlete požadavek. Metoda post by měla vrátit stavový kód 201 potvrzení úspěšného zpracování. 

   Buďte připravení na to, že může trvat i několik minut, než se tento krok dokončí. I když je sada dat malá, analytické dovednosti jsou výpočetně náročné. 

> [!NOTE]
> Vytvoření indexeru vyvolá kanál. Pokud dojde k nějakému problému při komunikaci s daty, při mapování vstupů a výstupů nebo s pořadím operací, zobrazí se v této fázi. Pokud chcete kanál spustit znovu s pozměněným kódem nebo skriptem, bude možná nutné nejdříve zahodit objekty. Další informace najdete v článku o [resetování a opětovném spuštění](#reset).

#### <a name="about-indexer-parameters"></a>O parametrech indexeru

Skript nastaví ```"maxFailedItems"``` na -1, což dá modulu indexování pokyn, aby během importování dat ignoroval chyby. To je přijatelné, protože v ukázkovém zdroji dat je k dispozici několik dokumentů. Pro větší zdroje dat by tato hodnota byla větší než 0.

Příkaz ```"dataToExtract":"contentAndMetadata"``` říká indexeru, aby automaticky rozbalí obsah z různých formátů souborů a také metadata týkající se jednotlivých souborů. 

Když se extrahuje obsah, můžete nastavit ```imageAction```, aby se z obrázků nalezených ve zdroji dat extrahoval text. Konfigurace ```"imageAction":"generateNormalizedImages"``` v kombinaci s dovedností rozpoznávání textu a dovedností pro sloučení textu říká indexeru, aby vyextrahovali text z obrázků (například slovo "Stop" z znaku zastavení provozu) a vložil jej jako součást pole Content. Toto chování platí jak pro obrázky vložené do dokumentů (třeba obrázek v souboru PDF), tak pro obrázky nalezené ve zdroji dat, např. soubor JPG.

## <a name="4---monitor-indexing"></a>4\. monitorování indexování

Indexování a obohacení začne ihned po odeslání žádosti o vytvoření indexeru. V závislosti na tom, jaké dovednosti rozpoznávání jste definovali, může indexování chvíli trvat. Pokud chcete zjistit, jestli indexer pořád běží, pošlete následující požadavek, který zjistí jeho stav.

1. Použijte **Get** a následující adresu URL, kde nahraďte název vaší služby skutečným názvem vaší služby a pojmenujte indexer.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2019-05-06
   ```

1. Přečtěte si odpověď, kde se dozvíte, jestli je indexer spuštěný, nebo jestli chcete zobrazit informace o chybách a upozorněních.  

Pokud používáte úroveň Free, očekává se následující zpráva: "" z dokumentu nelze extrahovat obsah nebo metadata. Zkrácený extrahovaný text na ' 32768 ' znaků. Tato zpráva se zobrazí, protože indexování objektů blob na bezplatné úrovni má[pro extrakci znaků limit 32 znaků](search-limits-quotas-capacity.md#indexer-limits). Tato zpráva se pro tuto sadu dat nezobrazuje na vyšších úrovních. 

> [!NOTE]
> V některých scénářích jsou běžné výstrahy a neoznačují se vždy problém. Pokud například kontejner objektů BLOB obsahuje soubory obrázků a kanál nezpracovává obrázky, zobrazí se upozornění oznamující, že image nebyly zpracovány.

## <a name="5---search"></a>5 – hledání

Teď, když jste vytvořili nová pole a informace, můžeme spustit některé dotazy, abychom porozuměli hodnotě hledání rozpoznávání v souvislosti s běžným scénářem hledání.

Odvoláme, že jsme začali s obsahem objektu blob, kde se celý dokument balí do jednoho `content` pole. Můžete hledat v tomto poli a vyhledat odpovídající dotazy.

1. Použijte příkaz **Get** a následující adresu URL, kde nahraďte název vaší služby skutečným názvem vaší služby, vyhledejte výskyty podmínky nebo fráze a vraťte pole `content` a počet odpovídajících dokumentů.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?search=*&$count=true&$select=content?api-version=2019-05-06
   ```
   
   Výsledky tohoto dotazu vrátí obsah dokumentu, což je stejný výsledek jako při použití indexeru objektů BLOB bez kanálu vyhledávání v rozpoznávání. Toto pole je možné prohledávat, ale nefunguje, pokud chcete použít omezující vlastnosti, filtry nebo automatické dokončování.

   ![Výstup pole obsahu](media/cognitive-search-tutorial-blob/content-output.png "Výstup pole obsahu")
   
1. Pro druhý dotaz vraťte některá z nových polí vytvořených kanálem (osoby, organizace, umístění, languageCode). Vynecháváme klíčová fráze pro zkrácení, ale pokud chcete zobrazit tyto hodnoty, měli byste je zahrnout.

   ```http
   https://mydemo.search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2019-05-06
   ```
   Pole v příkazu $select obsahují nové informace vytvořené z možností zpracování v přirozeném jazyce Cognitive Services. Jak byste mohli očekávat, došlo k nějakému šumu ve výsledcích a variaci v rámci dokumentů, ale v mnoha instancích analytické modely poskytují přesné výsledky.

   Následující obrázek ukazuje výsledky otevřeného dopisu Satya Nadella po předpokladu role generálního ředitele v Microsoftu.

   ![Výstup kanálu](media/cognitive-search-tutorial-blob/pipeline-output.png "Výstup kanálu")

1. Chcete-li zjistit, jak byste mohli využít tato pole, přidejte parametr omezující podmínky, který vrátí agregaci vyhovujících dokumentů podle umístění.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2019-05-06
   ``` 

   V tomto příkladu pro každé umístění existuje 2 nebo 3 shoda.

   ![Výstup omezující vlastnosti](media/cognitive-search-tutorial-blob/facet-output.png "Výstup omezující vlastnosti")
   

1. V tomto konečném příkladu použijte filtr pro kolekci organizace a vraťte dvě shody pro kritéria filtru založené na NASDAQ.

   ```http
   cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2019-05-06
   ```

Tyto dotazy znázorňují několik způsobů, jak můžete pracovat se syntaxí dotazů a filtry pro nová pole vytvořená pomocí rozpoznávání rozpoznávání. Další příklady dotazů naleznete v tématu [Příklady v dokumentu hledání REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples), [jednoduché příklady dotazů syntaxe](search-query-simple-examples.md)a [úplné příklady dotazů na Lucene](search-query-lucene-examples.md).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

V počátečních fázích vývoje je praktické odstranit objekty z Azure Kognitivní hledání a nechat si kód znovu sestavit. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Postup opětovného indexování dokumentů s novými definicemi:

1. Odstraňte indexer, index a dovednosti.
2. Upravte definice objektů.
3. Znovu vytvořte objekty ve vaší službě. Při opětovném vytváření indexeru se spustí kanál. 

Portál **můžete použít k** odstranění indexů, indexerů a dovednosti nebo k zadání adresy URL pro každý objekt. Následující příkaz odstraní indexer.

```http
DELETE https://[YOUR-SERVICE-NAME]].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
```

Při úspěšném odstranění se vrátí kód stavu 204.

## <a name="takeaways"></a>Shrnutí

Tento kurz ukazuje základní postup sestavení kanálu pro rozšířené indexování, při kterém se vytvářejí součásti: zdroj dat, sada dovedností, index a indexer.

Byly představeny [předdefinované dovednosti](cognitive-search-predefined-skills.md) společně s definicí dovednosti a mechanismy zřetězení dovedností prostřednictvím vstupů a výstupů. Zjistili jste taky, že `outputFieldMappings` v definici indexeru je potřeba pro směrování hodnot obohacených z kanálu do indexu s možností vyhledávání ve službě Azure Kognitivní hledání.

Nakonec jste se dozvěděli, jak testovat výsledky a resetovat systém pro další iterace. Zjistili jste, že zasílání dotazů na index vrací výstup vytvořený kanálem rozšířeného indexování. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejší způsob vyčištění po kurzu odstraněním skupiny prostředků, která obsahuje službu Azure Kognitivní hledání a Azure Blob service. Za předpokladu, že jste vložili obě služby do stejné skupiny, odstraňte skupinu prostředků. Tím se trvale odstraní všechno, co v ní je, včetně služeb a veškerého uloženého obsahu, který jste v tomto kurzu vytvořili. Na portálu najdete název skupiny prostředků na stránce Přehled jednotlivých služeb.

## <a name="next-steps"></a>Další kroky

Pokud si chcete kanál přizpůsobit nebo rozšířit, můžete použít vlastní dovednosti. Když si vytvoříte vlastní dovednost, kterou pak přidáte do sady dovedností, budete moct používat analýzu textu a obrazu, kterou si sami napíšete. 

> [!div class="nextstepaction"]
> [Příklad: Vytvoření vlastní dovednosti pro obohacení AI](cognitive-search-create-custom-skill-example.md)