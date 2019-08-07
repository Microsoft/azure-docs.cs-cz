---
title: 'Kurz REST: Volání Cognitive Services v kanálu rozšíření AI – Azure Search'
description: Projděte si příklad extrakce dat, přirozeného jazyka a zpracování souborů AI v Azure Search indexování pro extrakci a transformaci dat přes objekty JSON JSON pomocí post a REST API.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: d431f0ced5b417e178e064dca347ae8d78f14e5d
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840875"
---
# <a name="rest-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>Kurz REST: Volání rozhraní API služeb Cognitive Services v kanálu indexování Azure Search

V tomto kurzu se naučíte, jak v Azure Search naprogramovat rozšiřování dat pomocí *kognitivních dovedností*. Dovednosti jsou zajištěny při zpracování přirozeného jazyka (NLP) a možností analýzy obrázků v Cognitive Services. Prostřednictvím složení a konfigurace dovednosti můžete extrahovat text a text reprezentace obrázku nebo naskenovaného souboru dokumentu. Můžete také zjistit jazyk, entity, klíčové fráze a další. Konečný výsledek je bohatě doplňující obsah v indexu Azure Search, který se vytvořil s obohacením AI v kanálu indexování. 

V tomto kurzu budete volat rozhraní REST API, aby provedlo tyto úlohy:

> [!div class="checklist"]
> * Vytvoření kanálu indexace, který rozšiřuje ukázková data na cestě k indexu
> * Použití integrovaných dovedností: rozpoznávání entit, zjišťování jazyka, manipulace s textem a extrakce klíčových frází
> * Zjištění, jak mapováním vstupů na výstupy v sadě dovedností řetězit dovednosti za sebe
> * Spouštění požadavků a kontrola výsledků
> * Resetování indexu a indexerů pro další vývoj

Výstup indexu v Azure Search, který se dá prohledávat fulltextově Index můžete rozšířit pomocí dalších standardních funkcí, jako jsou [synonyma](search-synonyms.md), [profily vyhodnocování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analyzátory](search-analyzers.md) a [filtry](search-filters.md).

Tento kurz běží na bezplatné službě, ale počet bezplatných transakcí je omezený na 20 dokumentů za den. Pokud chcete spustit tento kurz ve stejný den více než jednou, použijte menší sadu souborů, abyste se mohli přizpůsobit více spuštění.

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů v Azure Search. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsány na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se používají následující služby, nástroje a data. 

+ [Vytvořte účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pro ukládání ukázkových dat. Ujistěte se, že je účet úložiště ve stejné oblasti jako Azure Search.

+ [Aplikace po pracovní ploše](https://www.getpostman.com/) se používá k provádění volání REST Azure Search.

+ [Ukázková data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) se skládají z malých souborových sad různých typů. 

+ [Vytvořte službu Azure Search](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento kurz můžete použít bezplatnou službu.

## <a name="get-a-key-and-url"></a>Získat klíč a adresu URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. Přihlaste se [k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení** > **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

![Získání koncového bodu http a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu http a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="prepare-sample-data"></a>Příprava ukázkových dat

Kanál pro rozšiřování načítá informace ze zdrojů dat Azure. Zdrojová data musí pocházet z podporovaného typu zdroje dat [indexeru Azure Search](search-indexer-overview.md). Pro vyhledávání rozpoznávání se nepodporuje Azure Table Storage. Při tomto cvičení použijeme službu Blob Storage, na které ukážeme několik typů obsahu.

1. Přihlaste se [k Azure Portal](https://portal.azure.com), přejděte k účtu úložiště Azure, klikněte na **objekty blob**a pak klikněte na **+ kontejner**.

1. [Vytvořte kontejner objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) , který bude obsahovat vzorová data. Úroveň veřejného přístupu můžete nastavit na libovolnou z jeho platných hodnot.

1. Po vytvoření kontejneru ho otevřete a na panelu příkazů vyberte **nahrát** a nahrajte ukázkové soubory, které jste stáhli v předchozím kroku.

   ![Zdrojové soubory ve službě Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Až se ukázkové soubory nahrají, získejte název kontejneru a připojovací řetězec vaší služby Blob Storage. Najdete je, když přejdete na účet úložiště na portálu Azure Portal. V části **Přístupové klíče** zkopírujte pole **Připojovací řetězec**.

   Připojovací řetězec by měl být adresa URL, která vypadá nějak takto:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Existují i jiné způsoby, jak zadat připojovací řetězec, například poskytnutím sdíleného přístupového podpisu. Další informace o přihlašovacích údajích ke zdroji dat najdete v článku o [indexování služby Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-postman"></a>Nastavení nástroje Postman

Spusťte Postman a nastavte požadavek HTTP. Pokud tento nástroj neznáte, přečtěte si téma [zkoumání Azure Search rozhraní REST API pomocí post](search-get-started-postman.md).

Metody žádosti použité v tomto kurzu jsou **post**, **Put**a **Get**. Klíče hlaviček jsou "Content-Type" nastavené na "Application/JSON" a "API-Key" nastavené na klíč správce služby Azure Search. Samotný obsah volání se vkládá do textu požadavku. 

  ![Prohledávání částečně strukturovaných dat](media/search-semi-structured-data/postmanoverview.png)

Používáme metodu POST k vytvoření zdroje dat, dovednosti, indexu a indexeru prostřednictvím rozhraní API služby Search. Zdroj dat obsahuje ukazatel na váš účet úložiště a vaše data JSON. Vaše služba Search se připojí při načítání dat.


## <a name="create-a-data-source"></a>Vytvoření zdroje dat

Teď, když už máte připravené služby a zdrojové soubory, můžete začít sestavovat součásti kanálu indexování. Začněte [objektem zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source), který službě Azure Search řekne, jak načíst externí zdrojová data.

Do hlavičky požadavku zadejte název služby, který jste použili při vytváření služby Azure Search, a klíč rozhraní API vygenerovaný pro vaši vyhledávací službu. Do textu požadavku zadejte název kontejneru objektů blob a připojovací řetězec.

### <a name="sample-request"></a>Ukázkový požadavek
```http
POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
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

Vzhledem k tomu, že je to váš první požadavek, podívejte se na portál Azure Portal a potvrďte, že se zdroj dat vytvořil ve službě Azure Search. Na stránce řídicí panel služby vyhledávání ověřte, že seznam zdrojů dat obsahuje novou položku. Možná bude nutné několik minut počkat, než se stránka portálu aktualizuje. 

  ![Dlaždice Zdroje dat na portálu](./media/cognitive-search-tutorial-blob/data-source-tile.png "Dlaždice Zdroje dat na portálu")

Pokud dostanete chybu 403 nebo 404, zkontrolujte vytvoření požadavku: v koncovém bodu by mělo být `api-version=2019-05-06`, v hlavičce za `Content-Type` by mělo být `api-key` a jeho hodnota musí být pro vyhledávací službu platná. Hlavičku budete moct znovu používat i v dalších krocích tohoto kurzu.

## <a name="create-a-skillset"></a>Vytvoření sady dovedností

V tomto kroku definujete postup rozšiřování, který chcete použít pro svá data. Jednotlivým krokům rozšiřování se říká *dovednosti*, zatímco sada kroků rozšiřování je *sada dovedností*. V tomto kurzu se používá integrovaný program pro [rozpoznávání hlasu](cognitive-search-predefined-skills.md) pro dovednosti:

+ [Rozpoznávání jazyka](cognitive-search-skill-language-detection.md), které identifikuje jazyk obsahu

+ [Rozdělení textu](cognitive-search-skill-textsplit.md), které před zavoláním dovednosti extrakce klíčových frází rozdělí velký obsah do menších bloků Extrakce klíčových frází přijímá vstup složený z 50 000 znaků nebo méně. Některé ze zdrojových souborů je nutné rozdělit, aby se do tohoto limitu vešly.

+ [Rozpoznávání entit](cognitive-search-skill-entity-recognition.md) pro extrahování názvů organizací z obsahu v kontejneru objektů BLOB.

+ [Extrakce klíčových frází](cognitive-search-skill-keyphrases.md), která získává hlavní klíčové fráze 

### <a name="sample-request"></a>Ukázkový požadavek
Než provedete toto volání rozhraní API, nezapomeňte v případě, že váš nástroj mezi voláními nezachovává hlavičku požadavku, nahradit v níže uvedeném požadavku název služby a klíč správce. 

Tento požadavek vytvoří sadu dovedností. Ve zbývající části tohoto kurzu se odkazujte na název sady dovedností ```demoskillset```.

```http
PUT https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2019-05-06
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
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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

#### <a name="explore-the-request-body"></a>Průzkum textu požadavku

Všimněte si, jak se na jednotlivých stránkách používá dovednost extrakce klíčových frází. Když nastavíte kontext na ```"document/pages/*"```, spustíte tento rozšiřovací nástroj pro každý člen pole dokumentu nebo stránek (pro každou stránku v dokumentu).

Pro obsah dokumentu se využijí jednotlivé dovednosti. Během zpracování služba Azure Search otevře každý dokument a přečte obsah z různých formátů souborů. Nalezený text, který pochází ze zdrojového souboru, se umístí do vygenerovaného pole ```content```, jednoho pro každý dokument. Proto nastavte vstup na ```"/document/content"```.

Grafickou reprezentaci sady dovedností najdete níže. 

![Ilustrace sady dovedností](media/cognitive-search-tutorial-blob/skillset.png "Ilustrace sady dovedností")

Výstupy se dají namapovat na index, použít jako vstup do podřízené dovednosti, nebo využít oběma způsoby tak, jak se to dělá s kódem jazyka. V indexu je kód jazyka užitečný při filtrování. Jako vstup se kód jazyka používá v dovednostech analýzy textu, čímž se jazykovým pravidlům poskytne informace o dělení slov.

Další informace o základních principech sady dovedností najdete v článku o [definování sady dovedností](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Vytvoření indexu

V tomto oddílu definujete schéma indexu, a to tak, že zadáte, která pole se mají zahrnout do prohledávatelného indexu, a pro jednotlivá pole poskytnete atributy vyhledávání. Pole mají typ a můžou přijímat argumenty, které určují, jak se pole používá (prohledávatelné, seřaditelné apod.). K vyhledání přesné shody s názvy polí ve zdroji není nutné, aby index tyto názvy polí obsahoval. V pozdějším kroku přidáte v indexeru mapování polí, pomocí kterých propojíte zdrojová a cílová pole. Pro tento krok definujte index pomocí konvencí pojmenování relevantních pro vaši vyhledávací aplikaci.

V tomto cvičení použijeme následující pole a jejich typy:

| Názvy polí: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| Typy polí: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="sample-request"></a>Ukázkový požadavek
Než provedete toto volání rozhraní API, nezapomeňte v případě, že váš nástroj mezi voláními nezachovává hlavičku požadavku, nahradit v níže uvedeném požadavku název služby a klíč správce. 

Tento požadavek vytvoří index. Ve zbývající části tohoto kurzu používejte název indexu ```demoindex```.

```http
PUT https://[servicename].search.windows.net/indexes/demoindex?api-version=2019-05-06
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

Do této chvíle jste vytvořili zdroj dat, sadu dovedností a index. Tyto tři komponenty se stanou součástí [indexeru](search-indexer-overview.md), který jednotlivé části sestaví do jediné operace s více fázemi. Pokud je chcete sloučit do indexeru, musíte nadefinovat mapování polí. 

+ FieldMappings se zpracovávají před dovednosti a mapuje zdrojová pole ze zdroje dat na cílová pole v indexu. Pokud jsou názvy polí a typy na obou koncích stejné, není nutné žádné mapování.

+ OutputFieldMappings se zpracovávají po dovednosti a odkazují na sourceFieldNames, které neexistují, dokud je nevytvoří dokument pro vyhodnocování nebo rozšíření. TargetFieldName je pole v indexu.

Kromě zapojení vstupů do výstupů můžete také použít mapování polí pro sloučení datových struktur. Další informace najdete v tématu [mapování obohacených polí na index s možností prohledávání](cognitive-search-output-field-mapping.md).

### <a name="sample-request"></a>Ukázkový požadavek

Než provedete toto volání rozhraní API, nezapomeňte v případě, že váš nástroj mezi voláními nezachovává hlavičku požadavku, nahradit v níže uvedeném požadavku název služby a klíč správce. 

Zadejte i název indexeru. Ve zbývající části tohoto kurzu se na něj můžete odkazovat názvem ```demoindexer```.

```http
PUT https://[servicename].search.windows.net/indexers/demoindexer?api-version=2019-05-06
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

#### <a name="explore-the-request-body"></a>Průzkum textu požadavku

Skript nastaví ```"maxFailedItems"``` na -1, což dá modulu indexování pokyn, aby během importování dat ignoroval chyby. To je užitečné, protože v ukázkovém zdroji dat je velmi málo dokumentů. Pro větší zdroje dat by tato hodnota byla větší než 0.

Navíc si všimněte příkazu ```"dataToExtract":"contentAndMetadata"``` v parametrech konfigurace. Tento příkaz dává indexeru pokyn, aby automaticky extrahoval obsah z různých formátů souborů, stejně jako metadata, která s jednotlivými soubory souvisí. 

Když se extrahuje obsah, můžete nastavit ```imageAction```, aby se z obrázků nalezených ve zdroji dat extrahoval text. V ```"imageAction":"generateNormalizedImages"``` kombinaci s dovedností optického textu a dovednosti pro sloučení textu říká indexeru, aby vyextrahovali text z obrázků (například slovo "Stop" z znaku zastavení provozu) a vloží ho jako součást pole Content. Toto chování platí jak pro obrázky vložené do dokumentů (třeba obrázek v souboru PDF), tak pro obrázky nalezené ve zdroji dat, např. soubor JPG.

## <a name="check-indexer-status"></a>Kontrola stavu indexeru

Až se indexer nadefinuje, automaticky se spustí, až se odešle požadavek. Podle kognitivních dovedností, které jste definovali, může indexování trvat déle, než jste čekali. Pokud chcete zjistit, jestli indexer pořád běží, pošlete následující požadavek, který zjistí jeho stav.

```http
GET https://[servicename].search.windows.net/indexers/demoindexer/status?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

Odpověď vám dá informaci o tom, jestli je indexer spuštěný. Až se indexování dokončí, použijte další operaci HTTP GET na koncový bod STATUS (jako výše) a zobrazí se vám sestava všech chyb a upozornění, ke kterým během rozšiřování došlo.  

Pro určité kombinace zdrojových souborů a dovedností jsou upozornění běžná a ne vždy představují problém. V tomto kurzu jsou upozornění neškodná (např. v souboru JPEG nejsou žádné textové vstupy). Podrobné informace o upozorněních vygenerovaných během indexování najdete v odpovědi stavu.
 
## <a name="query-your-index"></a>Dotázání indexu

Jakmile se indexování dokončí, spusťte dotazy, které vrátí obsah jednotlivých polí. Standardně služba Azure Search vrací prvních 50 výsledků. Ukázková data jsou malá, takže výchozí nastavení stačí. Až ale budete pracovat s většími sadami dat, budete možná potřebovat přidat do řetězce dotazu nějaké parametry, aby se výsledků vrátilo více. Pokyny najdete v článku [Jak v Azure Search stránkovat výsledky hledání](search-pagination-page-layout.md).

Pro ověření pošlete indexu dotaz na všechna pole.

```http
GET https://[servicename].search.windows.net/indexes/demoindex?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

Výstupem je schéma indexu s názvem, typem a atributy všech jednotlivých polí.

Pošlete druhý dotaz pro `"*"`, aby se vrátil všechen obsah jednoho pole, třeba `organizations`.

```http
GET https://[servicename].search.windows.net/indexes/demoindex/docs?search=*&$select=organizations&api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

Opakujte pro další pole: obsah, languageCode, klíčová fráze a organizace v tomto cvičení. Prostřednictvím `$select` můžete pomocí seznamu hodnot oddělených čárkami vrátit více než jedno pole.

V závislosti na složitosti a délce řetězce dotazu můžete použít operace GET nebo POST. Další informace najdete v článku o [dotazování pomocí rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).



<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

V raných experimentálních fázích vývoje kanálu je při iterování návrhu nejpraktičtější, když se ze služby Azure Search odstraní objekty a umožní se kódu, aby je znovu vytvořil. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Pokud chcete dokumenty znovu indexovat s novými definicemi:

1. Odstraňte index, aby se odebrala trvalá data. Odstraňte indexer a znovu ho ve službě vytvořte.
2. Upravte sadu dovedností a definici indexu.
3. Znovu ve službě vytvořte index a indexer, abyste mohli spustit kanál. 

Portál můžete použít k odstranění indexů, indexerů a dovednosti.

```http
DELETE https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2019-05-06
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
> [Příklad: Vytvoření vlastní dovednosti pro hledání vnímání](cognitive-search-create-custom-skill-example.md)
