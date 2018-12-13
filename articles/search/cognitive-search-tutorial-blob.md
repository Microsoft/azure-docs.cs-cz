---
title: Kurz pro volání kognitivního vyhledávání rozhraní API – Azure Search
description: Tento kurz vás krok za krokem provede ukázkovou extrakcí dat a zpracováním přirozeného jazyka a obrázků pomocí umělé inteligence v indexování Azure Search s cílem extrahovat a transformovat data.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 4f5b0661f67dd63177309905079ee68716e9e721
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315736"
---
# <a name="tutorial-learn-how-to-call-cognitive-search-apis-preview"></a>Kurz: Zjistěte, jak volat kognitivních hledání rozhraní API (Preview)

V tomto kurzu se naučíte, jak v Azure Search naprogramovat rozšiřování dat pomocí *kognitivních dovedností*. Kognitivní dovednosti jsou operace zpracování přirozeného jazyka (NLP) a analýzy obrazu, které extrahují text a textové reprezentace z obrázku, zjišťují jazyk, entity, klíčové fráze apod. Výsledkem je rozsáhlý doprovodný obsah v indexu Azure Search, který se vytvořil pomocí kanálu indexování kognitivního hledání. 

V tomto kurzu budete volat rozhraní REST API, aby provedlo tyto úlohy:

> [!div class="checklist"]
> * Vytvoření kanálu indexace, který rozšiřuje ukázková data na cestě k indexu
> * Použití integrovaných dovedností: rozpoznávání entit, zjišťování jazyka, manipulace s textem a extrakce klíčových frází
> * Zjištění, jak mapováním vstupů na výstupy v sadě dovedností řetězit dovednosti za sebe
> * Spouštění požadavků a kontrola výsledků
> * Resetování indexu a indexerů pro další vývoj

Výstup indexu v Azure Search, který se dá prohledávat fulltextově Index můžete rozšířit pomocí dalších standardních funkcí, jako jsou [synonyma](search-synonyms.md), [profily vyhodnocování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analyzátory](search-analyzers.md) a [filtry](search-filters.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!NOTE]
> Od 21. prosince 2018 se budou moct přidružit dovednosti Azure Search prostředku služeb Cognitive Services. To vám umožní spouštění poplatků za využití jeho dovedností. K tomuto datu také začneme pro extrakci image jako součást fáze hádání dokumentu. Extrakce textu z dokumentů se bude dál nabízet bez dalších poplatků.
>
> Provádění předdefinované dovednosti budou účtovat stávající [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services/) . Ceny za extrakce Image se bude účtovat ceny verze preview a je popsaný na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Přečtěte si [Další](cognitive-search-attach-cognitive-services.md).

## <a name="prerequisites"></a>Požadavky

Ještě kognitivní hledání neznáte? Přečtěte si článek [Co je kognitivní hledání?](cognitive-search-concept-intro.md), kde se s ním můžete seznámit, nebo si vyzkoušejte [rychlý start na portálu](cognitive-search-quickstart-blob.md), který nabízí praktické úvodní informace o důležitých konceptech.

Abyste mohli volat rozhraní REST v Azure Search, formulujte požadavky HTTP pomocí PowerShellu nebo webového testovacího nástroje, třeba Telerik Fiddleru nebo Postmanu. Pokud tyto nástroje ještě neznáte, přečtěte si článek [Zkoumání rozhraní REST API služby Azure Search pomocí nástroje Fiddler nebo Postman](search-fiddler.md).

Na portálu [Azure Portal](https://portal.azure.com/) můžete vytvářet služby, které se používají v celém pracovním postupu. 

### <a name="set-up-azure-search"></a>Nastavení služby Azure Search

Nejdříve se do služby Azure Search přihlaste. 

1. Přejděte na [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

1. Klikněte na **Vytvořit prostředek**, vyhledejte Azure Search a klikněte na **Vytvořit**. Pokud službu Search nastavujete poprvé, přečtěte si informace v článku [Vytvoření služby Azure Search na portálu](search-create-service-portal.md).

  ![Portál řídicího panelu](./media/cognitive-search-tutorial-blob/create-search-service-full-portal.png "Vytvoření služby Azure Search na portálu")

1. V části Skupina prostředků vytvořte skupinu prostředků, která bude obsahovat všechny prostředky vytvořené během tohoto kurzu. To usnadní čištění prostředků, až kurz dokončíte.

1. Pro umístění, zvolte jednu z [podporované oblasti](https://docs.microsoft.com/en-us/azure/search/cognitive-search-quickstart-blob#supported-regions) pro Kognitivního vyhledávání.

1. V části Cenová úroveň můžete pro účely kurzů a rychlých startů vytvořit bezplatnou (**Free**) službu. Pokud chcete důkladněji zkoumat svá vlastní data, vytvořte si [placenou službu](https://azure.microsoft.com/pricing/details/search/), např. **Basic** nebo **Standard**. 

  Bezplatná služba je omezená na 3 indexy, 16 MB maximální velikosti objektu blob a 2 minuty indexování, což pro úplné využití funkcí kognitivního hledání nestačí. Pokud se chcete podívat na limity pro jednotlivé úrovně, najdete je v článku o [limitech služeb](search-limits-quotas-capacity.md).

  ![Stránka definice služby na portálu](./media/cognitive-search-tutorial-blob/create-search-service1.png "stránku definice služby na portálu")
  ![stránku definice služby na portálu](./media/cognitive-search-tutorial-blob/create-search-service2.png "stránku definice služby v na portálu")

 
1. Pokud chcete mít k informacím o službě rychlý přístup, připněte si službu na řídicí panel.

  ![Stránka definice služby na portálu](./media/cognitive-search-tutorial-blob/create-search-service3.png "Stránka definice služby na portálu")

1. Po vytvoření služby, shromážděte následující informace: **Adresa URL** na stránce Přehled a **klíč api-key** (primární nebo sekundární) ze stránky klíče.

  ![Informace o koncovém bodu a klíči na portálu](./media/cognitive-search-tutorial-blob/create-search-collect-info.png "Informace o koncovém bodu a klíči na portálu")

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Nastavení služby Azure Blob a načtení ukázkových dat

Kanál pro rozšiřování načítá informace ze zdrojů dat Azure. Zdrojová data musí pocházet z podporovaného typu zdroje dat [indexeru Azure Search](search-indexer-overview.md). Při tomto cvičení použijeme službu Blob Storage, na které ukážeme několik typů obsahu.

1. [Stáhněte si ukázková data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4). Ukázková data sestávají z malé sady souborů různých typů. 

1. Zaregistruje si službu Azure Blob Storage, vytvořte si účet úložiště, pak se přihlaste do Průzkumníka služby Storage a vytvořte kontejner s názvem `basicdemo`. Pokyny ke všem těmto krokům najdete v článku o [rychlém startu s Průzkumníkem služby Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

1. V Průzkumníkovi služby Azure Storage v kontejneru `basicdemo`, který jste vytvořili, klikněte na **Nahrát** a nahrajte ukázkové soubory.

1. Až se ukázkové soubory nahrají, získejte název kontejneru a připojovací řetězec vaší služby Blob Storage. Najdete je, když přejdete na účet úložiště na portálu Azure Portal. V části **Přístupové klíče** zkopírujte pole **Připojovací řetězec**.

  Připojovací řetězec by měl být adresa URL, která vypadá nějak takto:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Existují i jiné způsoby, jak zadat připojovací řetězec, například poskytnutím sdíleného přístupového podpisu. Další informace o přihlašovacích údajích ke zdroji dat najdete v článku o [indexování služby Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="create-a-data-source"></a>Vytvoření zdroje dat

Teď, když už máte připravené služby a zdrojové soubory, můžete začít sestavovat součásti kanálu indexování. Začněte [objektem zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source), který službě Azure Search řekne, jak načíst externí zdrojová data.

Pro tento kurz použijte rozhraní REST API a nástroj, který dokáže formulovat a posílat požadavky HTTP, např. PowerShell, Postman nebo Fiddler. Do hlavičky požadavku zadejte název služby, který jste použili při vytváření služby Azure Search, a klíč rozhraní API vygenerovaný pro vaši vyhledávací službu. Do textu požadavku zadejte název kontejneru objektů blob a připojovací řetězec.

### <a name="sample-request"></a>Ukázkový požadavek
```http
POST https://[service name].search.windows.net/datasources?api-version=2017-11-11-Preview
Content-Type: application/json  
api-key: [admin key]  
```
#### <a name="request-body-syntax"></a>Syntaxe textu požadavku
```json
{   
    "name" : "demodata",  
    "description" : "Demo files to demonstrate cognitive search capabilities.",  
    "type" : "azureblob",
    "credentials" :
    { "connectionString" :
      "DefaultEndpointsProtocol=https;AccountName=<your account name>;AccountKey=<your account key>;"
    },  
    "container" : { "name" : "<your blob container name>" }
}  
```
Odešlete požadavek. Webový testovací nástroj by měl vrátit kód stavu 201, čímž potvrdí úspěšné provedení. 

Vzhledem k tomu, že je to váš první požadavek, podívejte se na portál Azure Portal a potvrďte, že se zdroj dat vytvořil ve službě Azure Search. Na stránce řídicího panelu vyhledávací služby ověřte, že dlaždice Zdroje dat má novou položku. Možná bude nutné několik minut počkat, než se stránka portálu aktualizuje. 

  ![Dlaždice Zdroje dat na portálu](./media/cognitive-search-tutorial-blob/data-source-tile.png "Dlaždice Zdroje dat na portálu")

Pokud dostanete chybu 403 nebo 404, zkontrolujte vytvoření požadavku: v koncovém bodu by mělo být `api-version=2017-11-11-Preview`, v hlavičce za `Content-Type` by mělo být `api-key` a jeho hodnota musí být pro vyhledávací službu platná. Hlavičku budete moct znovu používat i v dalších krocích tohoto kurzu.

> [!TIP]
> Teď než se pustíte do velké množství práce, je vhodná doba k ověření, že vyhledávací služba je spuštěná v jednom z podporovaných umístění poskytuje funkce ve verzi preview: Střední část jihu USA a západní Evropa.

## <a name="create-a-skillset"></a>Vytvoření sady dovedností

V tomto kroku definujete postup rozšiřování, který chcete použít pro svá data. Jednotlivým krokům rozšiřování se říká *dovednosti*, zatímco sada kroků rozšiřování je *sada dovedností*. V tomto kurzu se pro sadu dovedností používají [předdefinované kognitivní dovednosti](cognitive-search-predefined-skills.md):

+ [Rozpoznávání jazyka](cognitive-search-skill-language-detection.md), které identifikuje jazyk obsahu

+ [Rozdělení textu](cognitive-search-skill-textsplit.md), které před zavoláním dovednosti extrakce klíčových frází rozdělí velký obsah do menších bloků Extrakce klíčových frází přijímá vstup složený z 50 000 znaků nebo méně. Některé ze zdrojových souborů je nutné rozdělit, aby se do tohoto limitu vešly.

+ [Rozpoznávání pojmenovaných entit](cognitive-search-skill-named-entity-recognition.md) pro extrakci názvů organizací z obsahu v kontejneru objektů blob

+ [Extrakce klíčových frází](cognitive-search-skill-keyphrases.md), která získává hlavní klíčové fráze 

### <a name="sample-request"></a>Ukázkový požadavek
Než provedete toto volání rozhraní API, nezapomeňte v případě, že váš nástroj mezi voláními nezachovává hlavičku požadavku, nahradit v níže uvedeném požadavku název služby a klíč správce. 

Tento požadavek vytvoří sadu dovedností. Ve zbývající části tohoto kurzu se odkazujte na název sady dovedností ```demoskillset```.

```http
PUT https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Syntaxe textu požadavku
```json
{
  "description": 
  "Extract entities, detect language and extract key-phrases",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text", "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations", "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
      "inputs": [
        {
          "name": "text", "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "languageCode",
          "targetName": "languageCode"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "textSplitMode" : "pages", 
      "maximumPageLength": 4000,
      "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      { 
        "name": "languageCode",
        "source": "/document/languageCode"
      }
    ],
    "outputs": [
      {
            "name": "textItems",
            "targetName": "pages"
      }
    ]
  },
  {
      "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
      "context": "/document/pages/*",
      "inputs": [
        {
          "name": "text", "source": "/document/pages/*"
        },
        {
          "name":"languageCode", "source": "/document/languageCode"
        }
      ],
      "outputs": [
        {
          "name": "keyPhrases",
          "targetName": "keyPhrases"
        }
      ]
    }
  ]
}
```

Odešlete požadavek. Webový testovací nástroj by měl vrátit kód stavu 201, čímž potvrdí úspěšné provedení. 

#### <a name="about-the-request"></a>O požadavku

Všimněte si, jak se na jednotlivých stránkách používá dovednost extrakce klíčových frází. Když nastavíte kontext na ```"document/pages/*"```, spustíte tento rozšiřovací nástroj pro každý člen pole dokumentu nebo stránek (pro každou stránku v dokumentu).

Pro obsah dokumentu se využijí jednotlivé dovednosti. Během zpracování služba Azure Search otevře každý dokument a přečte obsah z různých formátů souborů. Nalezený text, který pochází ze zdrojového souboru, se umístí do vygenerovaného pole ```content```, jednoho pro každý dokument. Proto nastavte vstup na ```"/document/content"```.

Grafickou reprezentaci sady dovedností najdete níže. 

![Ilustrace sady dovedností](media/cognitive-search-tutorial-blob/skillset.png "Ilustrace sady dovedností")

Výstupy se dají namapovat na index, použít jako vstup do podřízené dovednosti, nebo využít oběma způsoby tak, jak se to dělá s kódem jazyka. V indexu je kód jazyka užitečný při filtrování. Jako vstup se kód jazyka používá v dovednostech analýzy textu, čímž se jazykovým pravidlům poskytne informace o dělení slov.

Další informace o základních principech sady dovedností najdete v článku o [definování sady dovedností](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Vytvoření indexu

V tomto oddílu definujete schéma indexu, a to tak, že zadáte, která pole se mají zahrnout do prohledávatelného indexu, a pro jednotlivá pole poskytnete atributy vyhledávání. Pole mají typ a můžou přijímat argumenty, které určují, jak se pole používá (prohledávatelné, seřaditelné apod.). K vyhledání přesné shody s názvy polí ve zdroji není nutné, aby index tyto názvy polí obsahoval. V pozdějším kroku přidáte v indexeru mapování polí, pomocí kterých propojíte zdrojová a cílová pole. Pro tento krok definujte index pomocí konvencí pojmenování relevantních pro vaši vyhledávací aplikaci.

V tomto cvičení použijeme následující pole a jejich typy:

| Názvy polí: | id       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| Typy polí: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="sample-request"></a>Ukázkový požadavek
Než provedete toto volání rozhraní API, nezapomeňte v případě, že váš nástroj mezi voláními nezachovává hlavičku požadavku, nahradit v níže uvedeném požadavku název služby a klíč správce. 

Tento požadavek vytvoří index. Ve zbývající části tohoto kurzu používejte název indexu ```demoindex```.

```http
PUT https://[servicename].search.windows.net/indexes/demoindex?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Syntaxe textu požadavku

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
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
Odešlete požadavek. Webový testovací nástroj by měl vrátit kód stavu 201, čímž potvrdí úspěšné provedení. 

Další informace o definování indexu najdete v článku o [vytvoření indexu (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).


## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Vytvoření indexeru, mapování polí a spouštění transformací

Do této chvíle jste vytvořili zdroj dat, sadu dovedností a index. Tyto tři komponenty se stanou součástí [indexeru](search-indexer-overview.md), který jednotlivé části sestaví do jediné operace s více fázemi. Pokud je chcete sloučit do indexeru, musíte nadefinovat mapování polí. Mapování polí jsou součástí definice indexeru a při odeslání požadavku spouštějí transformace.

Při indexování bez rozšiřování v případě, že se názvy polí a datové typy přesně neshodují nebo že chcete použít nějakou funkci, nabízí definice indexeru nepovinný oddíl *fieldMappings*.

V úlohách kognitivního hledání, které mají kanál pro rozšiřování, indexer vyžaduje *outputFieldMappings*. Tato mapování se použijí v případě, že zdrojem hodnot polí je interní proces (kanál pro rozšiřování). Mezi chování, která jsou pro *outputFieldMappings* jedinečná, patří možnost zpracovat komplexní typy vytvořené v rámci rozšiřování (prostřednictvím dovednosti Shaper). V jednom dokumentu může být navíc mnoho elementů (třeba více organizací v jednom dokumentu). Konstrukce *outputFieldMappings* může dát systému pokyn, aby kolekce elementů sloučil do jednoho záznamu.

### <a name="sample-request"></a>Ukázkový požadavek

Než provedete toto volání rozhraní API, nezapomeňte v případě, že váš nástroj mezi voláními nezachovává hlavičku požadavku, nahradit v níže uvedeném požadavku název služby a klíč správce. 

Zadejte i název indexeru. Ve zbývající části tohoto kurzu se na něj můžete odkazovat názvem ```demoindexer```.

```http
PUT https://[servicename].search.windows.net/indexers/demoindexer?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Syntaxe textu požadavku

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
        {
          "sourceFieldName" : "metadata_storage_path",
          "targetFieldName" : "id",
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
          "sourceFieldName" : "/document/organizations", 
          "targetFieldName" : "organizations"
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
        "imageAction": "generateNormalizedImages"
        }
  }
}
```

Odešlete požadavek. Webový testovací nástroj by měl vrátit kód stavu 201, čímž potvrdí úspěšné zpracování. 

Buďte připravení na to, že může trvat i několik minut, než se tento krok dokončí. I když je sada dat malá, analytické dovednosti jsou výpočetně náročné. Některé dovednosti, třeba analýza obrazu, trvají dlouho.

> [!TIP]
> Vytvoření indexeru vyvolá kanál. Pokud dojde k nějakému problému při komunikaci s daty, při mapování vstupů a výstupů nebo s pořadím operací, zobrazí se v této fázi. Pokud chcete kanál spustit znovu s pozměněným kódem nebo skriptem, bude možná nutné nejdříve zahodit objekty. Další informace najdete v článku o [resetování a opětovném spuštění](#reset).

### <a name="explore-the-request-body"></a>Průzkum textu požadavku

Skript nastaví ```"maxFailedItems"``` na -1, což dá modulu indexování pokyn, aby během importování dat ignoroval chyby. To je užitečné, protože v ukázkovém zdroji dat je velmi málo dokumentů. Pro větší zdroje dat by tato hodnota byla větší než 0.

Navíc si všimněte příkazu ```"dataToExtract":"contentAndMetadata"``` v parametrech konfigurace. Tento příkaz dává indexeru pokyn, aby automaticky extrahoval obsah z různých formátů souborů, stejně jako metadata, která s jednotlivými soubory souvisí. 

Když se extrahuje obsah, můžete nastavit ```ImageAction```, aby se z obrázků nalezených ve zdroji dat extrahoval text. ```"ImageAction":"generateNormalizedImages"``` dá indexeru pokyn extrahovat text z obrázků (například slovo stop z dopravní značky Stop) a vložit ho jako součást pole obsahu. Toto chování platí jak pro obrázky vložené do dokumentů (třeba obrázek v souboru PDF), tak pro obrázky nalezené ve zdroji dat, např. soubor JPG.

V této verzi Preview je ```"generateNormalizedImages"``` jediná platná hodnota pro ```"ImageAction"```.

## <a name="check-indexer-status"></a>Kontrola stavu indexeru

Až se indexer nadefinuje, automaticky se spustí, až se odešle požadavek. Podle kognitivních dovedností, které jste definovali, může indexování trvat déle, než jste čekali. Pokud chcete zjistit, jestli indexer pořád běží, pošlete následující požadavek, který zjistí jeho stav.

```http
GET https://[servicename].search.windows.net/indexers/demoindexer/status?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

Odpověď vám dá informaci o tom, jestli je indexer spuštěný. Až se indexování dokončí, použijte další operaci HTTP GET na koncový bod STATUS (jako výše) a zobrazí se vám sestava všech chyb a upozornění, ke kterým během rozšiřování došlo.  

Pro určité kombinace zdrojových souborů a dovedností jsou upozornění běžná a ne vždy představují problém. V tomto kurzu jsou upozornění neškodná (např. v souboru JPEG nejsou žádné textové vstupy). Podrobné informace o upozorněních vygenerovaných během indexování najdete v odpovědi stavu.
 
## <a name="verify-content"></a>Ověření obsahu

Jakmile se indexování dokončí, spusťte dotazy, které vrátí obsah jednotlivých polí. Standardně služba Azure Search vrací prvních 50 výsledků. Ukázková data jsou malá, takže výchozí nastavení stačí. Až ale budete pracovat s většími sadami dat, budete možná potřebovat přidat do řetězce dotazu nějaké parametry, aby se výsledků vrátilo více. Pokyny najdete v článku [Jak v Azure Search stránkovat výsledky hledání](search-pagination-page-layout.md).

Pro ověření pošlete indexu dotaz na všechna pole.

```http
GET https://[servicename].search.windows.net/indexes/demoindex?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

Výstupem je schéma indexu s názvem, typem a atributy všech jednotlivých polí.

Pošlete druhý dotaz pro `"*"`, aby se vrátil všechen obsah jednoho pole, třeba `organizations`.

```http
GET https://[servicename].search.windows.net/indexes/demoindex/docs?search=*&$select=organizations&api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

Tento postup opakujte pro další pole v tomto cvičení: content, language, keyphrases a organizations. Prostřednictvím `$select` můžete pomocí seznamu hodnot oddělených čárkami vrátit více než jedno pole.

V závislosti na složitosti a délce řetězce dotazu můžete použít operace GET nebo POST. Další informace najdete v článku o [dotazování pomocí rozhraní REST API](https://docs.microsoft.com/azure/search/search-query-rest-api).

<a name="access-enriched-document"></a>

## <a name="accessing-the-enriched-document"></a>Přístup k rozšířenému dokumentu

Kognitivní hledání umožňuje zobrazit si strukturu rozšířeného dokumentu. Rozšířené dokumenty jsou dočasné struktury, které se vytvářejí během rozšiřování a po dokončení procesu se odstraňují.

Pokud chcete zachytit snímek rozšířeného dokumentu vytvořeného během indexování, přidejte do indexu pole s názvem ```enriched```. Indexer do tohoto pole automaticky vypíše řetězcovou reprezentaci všech rozšíření daného dokumentu.

Pole ```enriched``` bude obsahovat řetězec, který je logickou reprezentací rozšířeného dokumentu uloženého v paměti ve formátu JSON.  Hodnota pole je ale platný dokument JSON. Abyste si mohli dokument zobrazit jako formátovaný dokument JSON, bude nutné nahradit `\"` za `"`, protože pro uvozovky se používají řídicí sekvence.  

Pole ```enriched``` existuje kvůli ladění, aby vám pomohlo pochopit logický formát obsahu, proti kterému se vyhodnocují výrazy. Může to být užitečný nástroj, který vám pomůže pochopit a ladit sadu dovedností.

Opakujte předchozí cvičení, včetně zachycení obsahu rozšířeného dokumentu pomocí pole `enriched`:

### <a name="request-body-syntax"></a>Syntaxe textu požadavku
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
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

V raných experimentálních fázích vývoje kanálu je při iterování návrhu nejpraktičtější, když se ze služby Azure Search odstraní objekty a umožní se kódu, aby je znovu vytvořil. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Pokud chcete dokumenty znovu indexovat s novými definicemi:

1. Odstraňte index, aby se odebrala trvalá data. Odstraňte indexer a znovu ho ve službě vytvořte.
2. Upravte sadu dovedností a definici indexu.
3. Znovu ve službě vytvořte index a indexer, abyste mohli spustit kanál. 

Na portálu můžete použít k odstranění indexy, indexery a dovednosti.

```http
DELETE https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

Při úspěšném odstranění se vrátí kód stavu 204.

Až se váš kód bude blížit dokončení, možná budete chtít zdokonalit strategii opětovného sestavování. Další informace najdete v článku o tom, [jak znovu sestavit index](search-howto-reindex.md).

## <a name="takeaways"></a>Shrnutí

Tento kurz ukazuje základní postup sestavení kanálu pro rozšířené indexování, při kterém se vytvářejí součásti: zdroj dat, sada dovedností, index a indexer.

Představili jsme si [předdefinované dovednosti](cognitive-search-predefined-skills.md), definice sad dovedností a mechanismy, jak pomocí vstupů a výstupů řetězit dovednosti za sebe. Naučili jste se, že `outputFieldMappings` v definici indexu umožňuje směrovat rozšířené hodnoty z kanálu do prohledávatelného indexu ve službě Azure Search.

Nakonec jste se dozvěděli, jak testovat výsledky a resetovat systém pro další iterace. Zjistili jste, že zasílání dotazů na index vrací výstup vytvořený kanálem rozšířeného indexování. V této verzi existuje mechanismus, jak si zobrazit vnitřní konstrukce (rozšířené dokumenty, které systém vytvořil). K tomu všemu jste se naučili, jak zkontrolovat stav indexeru a které objekty se mají před opětovným spuštěním kanálu odstranit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejší způsob, jak po kurzu vše vyčistit, je odstranit skupinu prostředků, která obsahuje službu Azure Search a službu Azure Blob. Za předpokladu, že jste vložili obě služby do stejné skupiny, odstraňte skupinu prostředků. Tím se trvale odstraní všechno, co v ní je, včetně služeb a veškerého uloženého obsahu, který jste v tomto kurzu vytvořili. Na portálu najdete název skupiny prostředků na stránce Přehled jednotlivých služeb.

## <a name="next-steps"></a>Další postup

Pokud si chcete kanál přizpůsobit nebo rozšířit, můžete použít vlastní dovednosti. Když si vytvoříte vlastní dovednost, kterou pak přidáte do sady dovedností, budete moct používat analýzu textu a obrazu, kterou si sami napíšete. 

> [!div class="nextstepaction"]
> [Příklad: vytvoření vlastní dovednosti](cognitive-search-create-custom-skill-example.md)
