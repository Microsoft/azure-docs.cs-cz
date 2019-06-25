---
title: 'Kurz k Pythonu: Volat služby Cognitive Services v kanál indexování – Azure Search'
description: Projděte skrze příkladem extrakce, přirozeného jazyka a image AI zpracování ve službě Azure Search pomocí poznámkového bloku Jupyter Python. Extrahovaná data je indexované a snadno přístupné dotazem.
manager: cgronlun
author: LisaLeib
services: search
ms.service: search
ms.devlang: python
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-lilei
ms.openlocfilehash: 50a252ff93f7e2cc6e5c6100c6bce850e9e96baf
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295625"
---
# <a name="python-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>Kurz k Pythonu: Volání rozhraní API služeb Cognitive Services v Azure Search indexování kanálu

V tomto kurzu se naučíte, jak v Azure Search naprogramovat rozšiřování dat pomocí *kognitivních dovedností*. Dovednosti využívají možnosti analýzy image ve službě Cognitive Services a zpracování přirozeného jazyka (NLP). Prostřednictvím složení dovednosti a konfigurace můžete extrahovat text a textové reprezentace obrázek nebo soubor naskenovaného dokumentu. Můžete také zjistit jazyk, entity, klíčové fráze a další. Výsledkem je formátovaný další obsah v indexu Azure Search, vytvořené pomocí AI obohacení v kanál indexování. 

V tomto kurzu použijete Pythonu a proveďte následující úlohy:

> [!div class="checklist"]
> * Vytvoření kanálu indexace, který rozšiřuje ukázková data na cestě k indexu
> * Použití integrovaných dovedností: rozpoznávání entit, zjišťování jazyka, manipulace s textem a extrakce klíčových frází
> * Zjištění, jak mapováním vstupů na výstupy v sadě dovedností řetězit dovednosti za sebe
> * Spouštění požadavků a kontrola výsledků
> * Resetování indexu a indexerů pro další vývoj

Výstup indexu v Azure Search, který se dá prohledávat fulltextově Index můžete rozšířit pomocí dalších standardních funkcí, jako jsou [synonyma](search-synonyms.md), [profily vyhodnocování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analyzátory](search-analyzers.md) a [filtry](search-filters.md). 

V tomto kurzu běží na bezplatné služby, ale počet transakcí zdarma je omezený na 20 dokumenty za den. Pokud chcete spustit v tomto kurzu více než jednou v jednom dni, použijte menší soubor nastavit tak, aby se vejde do více spuštění.

> [!NOTE]
> Jak můžete rozšířit rozsah zvýšení četnosti zpracování, přidání více dokumentů nebo přidání další algoritmy AI, budete muset [připojení účtovaných prostředku služeb Cognitive Services](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API ve službě Cognitive Services a extrakci image jako součást fáze hádání dokumentu ve službě Azure Search. Neúčtují žádné poplatky pro extrakci textu z dokumentů.
>
> Provádění předdefinované dovednosti, se účtuje za stávající [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakce Image je popsaný na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Následující služby, nástroje a data se používají v tomto kurzu. 

+ [Vytvoření účtu služby Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pro ukládání ukázková data. Ujistěte se, že je účet úložiště ve stejné oblasti jako Azure Search.

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), poskytování Python 3.x a poznámkové bloky Jupyter.

+ [Ukázková data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) sestává ze sady malý soubor různých typů. 

+ [Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službu pro účely tohoto kurzu.

## <a name="get-a-key-and-url"></a>Získejte klíč a adresy URL

K interakci s vaší službou Azure Search, budete potřebovat adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/)a ve vyhledávací službě **přehled** stránce, získat adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V **nastavení** > **klíče**, získat klíč pro úplná práva správce na službu. Existují dva klíče zaměnitelné správce, v případě, že budete potřebovat k výměně jeden k dispozici zajišťuje nepřetržitý chod podniků. U požadavků můžete použít buď primární nebo sekundární klíč pro přidání, úpravy a odstraňování objektů.

![Získejte koncový bod a přístupový klíč rozhraní HTTP](media/search-fiddler/get-url-key.png "získat HTTP koncový bod a přístupový klíč")

Všechny požadavky vyžaduje klíč rozhraní api na každou požadavku odeslaného do vaší služby. Platný klíč vytváří vztah důvěryhodnosti, na základě požadavku na mezi aplikace odešle požadavek a službu, která ji zpracovává.

## <a name="prepare-sample-data"></a>Příprava ukázkových dat

Kanál pro rozšiřování načítá informace ze zdrojů dat Azure. Zdrojová data musí pocházet z podporovaného typu zdroje dat [indexeru Azure Search](search-indexer-overview.md). Azure Table Storage se nepodporuje pro kognitivního vyhledávání. Při tomto cvičení použijeme službu Blob Storage, na které ukážeme několik typů obsahu.

1. [Přihlaste se k webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště Azure, klikněte na tlačítko **objekty BLOB**a potom klikněte na tlačítko **+ kontejner**.

1. [Vytvořte kontejner objektů Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) obsahuje ukázková data. Můžete nastavit úroveň veřejného přístupu k některému z jeho platných hodnot.

1. Po vytvoření kontejneru ho otevřete a vyberte **nahrát** na panelu příkazů pro nahrání ukázkových souborů, které jste si stáhli v předchozím kroku.

   ![Zdrojové soubory ve službě Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Až se ukázkové soubory nahrají, získejte název kontejneru a připojovací řetězec vaší služby Blob Storage. Najdete je, když přejdete na účet úložiště na portálu Azure Portal. Klikněte na tlačítko **přístupové klíče**a zkopírujte **připojovací řetězec** pole.

Připojovací řetězec bude mít tento formát: `DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Připojovací řetězec uchovávat po ruce. Budete ho potřebovat v dalším kroku.

Existují i jiné způsoby, jak zadat připojovací řetězec, například poskytnutím sdíleného přístupového podpisu. Další informace o přihlašovacích údajích ke zdroji dat najdete v článku o [indexování služby Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="create-a-jupyter-notebook"></a>Vytvoření poznámkového bloku Jupyter

> [!Note]
> Tento článek ukazuje, jak vytvořit zdroj dat, index, indexer a dovednosti pomocí skriptů Pythonu a řady. Stáhnout příklad kompletní Poznámkový blok, přejděte [úložiště Azure-Search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment-Jupyter-Notebook).

Spusťte Poznámkový blok Jupyter a vytvořte nový poznámkový blok Python 3 pomocí navigátoru Anaconda.

## <a name="connect-to-azure-search"></a>Připojení ke službě Azure Search

V poznámkovém bloku spusťte tento skript k načtení knihovny použité pro práci s JSON a formulování požadavků HTTP.

```python
import json
import requests
from pprint import pprint
```

Dále definujte názvy pro zdroj dat, index, indexer a dovedností. Spusťte tento skript k nastavení názvy pro účely tohoto kurzu.

```python
#Define the names for the data source, skillset, index and indexer
datasource_name="cogsrch-py-datasource"
skillset_name="cogsrch-py-skillset"
index_name="cogsrch-py-index"
indexer_name="cogsrch-py-indexer"
```

> [!Tip]
> Na bezplatné služby jste omezeni na tři indexy, indexery a zdroje dat. V tomto kurzu se vytváří od každého jeden. Ujistěte se, že existuje místo pro vytváření nových objektů před pokračováním žádné.

V následujícím skriptu nahraďte zástupné symboly pro vaši vyhledávací službu (YOUR-SEARCH-SERVICE-NAME) a rozhraní API pro správu klíčů (YOUR-ADMIN-API-KEY) a spusťte jej nastavit koncový bod vyhledávací služby.

```python
#Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
        'api-key': '<YOUR-ADMIN-API-KEY>' }
params = {
    'api-version': '2019-05-06'
}
```

## <a name="create-a-data-source"></a>Vytvoření zdroje dat

Teď, když už máte připravené služby a zdrojové soubory, můžete začít sestavovat součásti kanálu indexování. Objekt zdroje dat, která říká službě Azure Search jak načíst externí zdroj dat začíná.

V následujícím skriptu nahraďte zástupný symbol svoje-BLOB-prostředků-CONNECTION-STRING připojovacím řetězcem pro objekt blob, který jste vytvořili v předchozím kroku. Potom spusťte skript, který chcete vytvořit zdroj dat s názvem `cogsrch-py-datasource`.

```python
#Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
    "connectionString": datasourceConnectionString
   },
    "container": {
     "name": "basic-demo-data-pr"
   }
}
r = requests.put( endpoint + "/datasources/" + datasource_name, data=json.dumps(datasource_payload), headers=headers, params=params )
print (r.status_code)
```

Požadavek by měl vrátit stavový kód 201 potvrzení úspěšné.

Na webu Azure Portal, na stránce řídicího panelu služby vyhledávání, ověřte, že cogsrch-py – zdroj dat zobrazený v **zdroje dat** seznamu. Klikněte na tlačítko **aktualizovat** aktualizujte stránku.

![Dlaždice Zdroje dat na portálu](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Dlaždice Zdroje dat na portálu")

## <a name="create-a-skillset"></a>Vytvoření sady dovedností

V tomto kroku definujete sadu rozšíření kroků a použijte k vašim datům. Jednotlivým krokům rozšiřování se říká *dovednosti*, zatímco sada kroků rozšiřování je *sada dovedností*. Tento kurz používá [integrované kognitivní dovednosti](cognitive-search-predefined-skills.md) pro zkušenostech:

+ [Rozpoznávání jazyka](cognitive-search-skill-language-detection.md), které identifikuje jazyk obsahu

+ [Rozdělení textu](cognitive-search-skill-textsplit.md), které před zavoláním dovednosti extrakce klíčových frází rozdělí velký obsah do menších bloků Extrakce klíčových frází přijímá vstup složený z 50 000 znaků nebo méně. Některé ze zdrojových souborů je nutné rozdělit, aby se do tohoto limitu vešly.

+ [Rozpoznávání entit](cognitive-search-skill-entity-recognition.md) pro extrahování názvů organizace z obsahu v kontejneru objektů blob.

+ [Extrakce klíčových frází](cognitive-search-skill-keyphrases.md), která získává hlavní klíčové fráze 

### <a name="python-script"></a>Skript v jazyce Python
Spusťte následující skript k vytvoření dovedností volá `cogsrch-py-skillset`.

```python
#Create a skillset
skillset_payload = {
  "name": skillset_name,
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

r = requests.put(endpoint + "/skillsets/" + skillset_name, data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

Požadavek by měl vrátit stavový kód 201 potvrzení úspěšné.

Dovednosti extrakce klíčových frází platí pro každou stránku. Nastavením kontextu na `"document/pages/*"`, spusťte tento enricher pro každého člena pole nebo stránky dokumentu (pro každou stránku v dokumentu).

Pro obsah dokumentu se využijí jednotlivé dovednosti. Během zpracování služba Azure Search otevře každý dokument a přečte obsah z různých formátů souborů. Text nalezeny ve zdrojovém souboru se umístí do `content` pole, jeden pro každý dokument. Proto nastavte vstupu jako `"/document/content"`.

Grafickou reprezentaci sady dovedností najdete níže.

![Ilustrace sady dovedností](media/cognitive-search-tutorial-blob/skillset.png "Ilustrace sady dovedností")

Výstupy lze mapovat na index, používá jako vstup pro příjem dat dovedností nebo obojí, jako je tomu u kód jazyka. V indexu je kód jazyka užitečný při filtrování. Jako vstup se kód jazyka používá v dovednostech analýzy textu, čímž se jazykovým pravidlům poskytne informace o dělení slov.

Další informace o základních principech sady dovedností najdete v článku o [definování sady dovedností](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Vytvoření indexu

V této části definujete schéma indexu určením polí pro zahrnutí do prohledávatelný index a nastavení vyhledávání atributů pro každé pole. Pole mají typ a můžou přijímat argumenty, které určují, jak se pole používá (prohledávatelné, seřaditelné apod.). K vyhledání přesné shody s názvy polí ve zdroji není nutné, aby index tyto názvy polí obsahoval. V pozdějším kroku přidáte v indexeru mapování polí, pomocí kterých propojíte zdrojová a cílová pole. Pro tento krok definujte index pomocí konvencí pojmenování relevantních pro vaši vyhledávací aplikaci.

V tomto cvičení použijeme následující pole a jejich typy:

| Názvy polí: | id         | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| Typy polí: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Spusťte tento skript k vytvoření indexu s názvem `cogsrch-py-index`.

```python
#Create an index
index_payload = {
    "name": index_name,
    "fields": [
      {
        "name": "id",
        "type": "Edm.String",
        "key": "true",
        "searchable": "true",
        "filterable": "false",
        "facetable": "false",
        "sortable": "true"
      },
      {
        "name": "content",
        "type": "Edm.String",
        "sortable": "false",
        "searchable": "true",
        "filterable": "false",
        "facetable": "false"
      },
      {
        "name": "languageCode",
        "type": "Edm.String",
        "searchable": "true",
        "filterable": "false",
        "facetable": "false"
      },
      {
        "name": "keyPhrases",
        "type": "Collection(Edm.String)",
        "searchable": "true",
        "filterable": "false",
        "facetable": "false"
      },
      {
        "name": "organizations",
        "type": "Collection(Edm.String)",
        "searchable": "true",
        "sortable": "false",
        "filterable": "false",
        "facetable": "false"
      }
   ]
}

r = requests.put(endpoint + "/indexes/" + index_name, data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

Požadavek by měl vrátit stavový kód 201 potvrzení úspěšné.

Další informace o definování indexu najdete v článku o [vytvoření indexu (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Vytvoření indexeru, mapování polí a spouštění transformací

Zatím jste vytvořili zdroji dat, dovedností a indexu. Tyto tři komponenty se stanou součástí [indexeru](search-indexer-overview.md), který jednotlivé části sestaví do jediné operace s více fázemi. Chcete-li sjednotit tyto objekty v indexeru, je nutné definovat mapování polí.

+ FieldMappings se zpracovávají před zkušenostech mapování zdroje polí ze zdroje dat do cílového pole v indexu. Pokud názvy polí a typů jsou stejná na obou koncích, není třeba žádné mapování.

+ Zpracování outputFieldMappings po dovednosti, odkazující na sourceFieldNames, které neexistují až do dokumentu hádání nebo rozšiřování vytvoří je. TargetFieldName je pole v indexu.

Kromě zapojování vstupy do výstupů, můžete také použít mapování polí na sloučit různé datové struktury. Další informace najdete v tématu [způsob mapování polí bohatších možností prohledávatelný index](cognitive-search-output-field-mapping.md).

Spusťte tento skript k vytvoření indexer s názvem `cogsrch-py-indexer`.

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
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

r = requests.put(endpoint + "/indexers/" + indexer_name, data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

Požadavek by měl rychle vrátit stavový kód 201, ale zpracování může trvat několik minut. I když je malá datová sada, analytické svoje znalosti nástrojů, jako je například Analýza obrázků, jsou náročné na výpočty a dobu trvat.

Použití [zkontrolovat stav indexer](#check-indexer-status) skript v další části a zjistěte, po dokončení procesu indexeru.

> [!TIP]
> Vytvoření indexeru vyvolá kanál. Pokud dojde k nějakému problému, přístup k datům, mapování vstupy a výstupy, nebo s pořadím operací, zobrazí se v této fázi. K opětovnému spuštění kanálu změn kódu nebo skriptu, budete muset nejprve odstranit objekty. Další informace najdete v článku o [resetování a opětovném spuštění](#reset).

#### <a name="explore-the-request-body"></a>Průzkum textu požadavku

Skript nastaví `"maxFailedItems"` na -1, což dá modulu indexování pokyn, aby během importování dat ignoroval chyby. To je užitečné, protože v ukázkovém zdroji dat je velmi málo dokumentů. Pro větší zdroje dat by tato hodnota byla větší než 0.

Navíc si všimněte příkazu `"dataToExtract":"contentAndMetadata"` v parametrech konfigurace. Tento příkaz sděluje indexer pro extrahování obsahu z jiné formáty souborů a metadatech souvisejících s každého souboru.

Když se extrahuje obsah, můžete nastavit `imageAction`, aby se z obrázků nalezených ve zdroji dat extrahoval text. `"imageAction":"generateNormalizedImages"` Konfigurace, v kombinaci s OCR znalostí a dovedností sloučení textu, říká indexeru k extrakci textu z bitové kopie (například word "stop" od provozu Stop znak) a vloží ho jako součást pole obsahu. Toto chování platí pro Image vložit v dokumentech (zvažte obrázku ve formátu PDF) i Image ve zdroji dat, například soubor .jpg.

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>Kontrola stavu indexeru

Až se indexer nadefinuje, automaticky se spustí, až se odešle požadavek. Podle kognitivních dovedností, které jste definovali, může indexování trvat déle, než jste čekali. Chcete-li zjistit, zda po dokončení zpracování indexer, spusťte následující skript.

```python
#Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name + "/status", headers=headers,params=params)
pprint(json.dumps(r.json(), indent=1))
```

V odpovědi sledování "lastResult" hodnot "stavu" a "čas". Pravidelně spouštět skript, který chcete zkontrolovat stav. Po dokončení indexeru stav bude nastaven na "ÚSPĚCH", "čas" bude zadána a odpověď bude obsahovat všechny chyby a varování, ke kterým došlo během rozšiřování.

![Vytvoření indexeru](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "vytvoření indexeru")

Pro určité kombinace zdrojových souborů a dovedností jsou upozornění běžná a ne vždy představují problém. V tomto kurzu se upozornění jsou neškodné. Například jeden ze souborů ve formátu JPEG, nesmí být text se zobrazí upozornění na tomto snímku obrazovky.

![Příklad upozornění indexer](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "příklad indexer upozornění")

## <a name="query-your-index"></a>Dotázání indexu

Jakmile se indexování dokončí, spusťte dotazy, které vrátí obsah jednotlivých polí. Standardně služba Azure Search vrací prvních 50 výsledků. Ukázková data jsou malá, takže výchozí nastavení stačí. Až ale budete pracovat s většími sadami dat, budete možná potřebovat přidat do řetězce dotazu nějaké parametry, aby se výsledků vrátilo více. Pokyny najdete v článku [Jak v Azure Search stránkovat výsledky hledání](search-pagination-page-layout.md).

Pro ověření pošlete indexu dotaz na všechna pole.

```python
#Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name, headers=headers,params=params)
pprint(json.dumps(r.json(), indent=1))
```

Výsledky by měly vypadat podobně jako v následujícím příkladu. Na snímku obrazovky vidíte pouze část odpovědi.

![Index dotazu pro všechna pole](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "dotazování indexu pro všechna pole")

Výstupem je schéma indexu s názvem, typem a atributy všech jednotlivých polí.

Pošlete druhý dotaz pro `"*"`, aby se vrátil všechen obsah jednoho pole, třeba `organizations`.

```python
#Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name + "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Výsledky by měly vypadat podobně jako v následujícím příkladu. Na snímku obrazovky vidíte pouze část odpovědi.

![Index dotazu pro obsah organizace](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "dotazovat index pro vrácení obsahu organizace")

Opakujte pro další pole: obsah, prostředí, keyPhrases a organizace v tomto cvičení. Prostřednictvím `$select` můžete pomocí seznamu hodnot oddělených čárkami vrátit více než jedno pole.

V závislosti na složitosti a délce řetězce dotazu můžete použít operace GET nebo POST. Další informace najdete v článku o [dotazování pomocí rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).
it <a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

V raných experimentálních fázích vývoje kanálu je při iterování návrhu nejpraktičtější, když se ze služby Azure Search odstraní objekty a umožní se kódu, aby je znovu vytvořil. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Pokud chcete dokumenty znovu indexovat s novými definicemi:

1. Odstraňte index, aby se odebrala trvalá data. Odstraňte indexer a znovu ho ve službě vytvořte.
2. Úprava definice sada dovedností a indexová.
3. Znovu ve službě vytvořte index a indexer, abyste mohli spustit kanál.

Na portálu můžete použít k odstranění indexy, indexery a dovednosti. Když odstraníte indexeru, volitelně, selektivní odstraněním indexu, dovednosti a datové zdroje ve stejnou dobu.

![Odstranit objekty hledání](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "hledání odstranit objekty na portálu")

Můžete také odstranit, je pomocí skriptu. Následující skript se odstraní dovednosti, které jsme vytvořili. Požadavek na odstranění index, indexer a zdroj dat můžete snadno upravit.

```python
#delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name, headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Až se váš kód bude blížit dokončení, možná budete chtít zdokonalit strategii opětovného sestavování. Další informace najdete v článku o tom, [jak znovu sestavit index](search-howto-reindex.md).

## <a name="takeaways"></a>Shrnutí

Tento kurz ukazuje základní postup sestavení kanálu pro rozšířené indexování, při kterém se vytvářejí součásti: zdroj dat, sada dovedností, index a indexer.

[Předdefinované dovednosti](cognitive-search-predefined-skills.md) zavedených spolu s definicí dovedností a způsob, jak řetězec dovednosti společně prostřednictvím vstupy a výstupy. Naučili jste se, že `outputFieldMappings` v definici indexu umožňuje směrovat rozšířené hodnoty z kanálu do prohledávatelného indexu ve službě Azure Search.

Nakonec jste zjistili, jak výsledky testů a obnovit systém, hledá další iterace. Zjistili jste, že zasílání dotazů na index vrací výstup vytvořený kanálem rozšířeného indexování. V této verzi existuje mechanismus, jak si zobrazit vnitřní konstrukce (rozšířené dokumenty, které systém vytvořil). Také jste se naučili jak zkontrolovat stav indexeru a které objekty musí být odstraněny před opětovným spuštěním kanálu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejší způsob, jak po kurzu vše vyčistit, je odstranit skupinu prostředků, která obsahuje službu Azure Search a službu Azure Blob. Za předpokladu, že obě služby umístíte do stejné skupiny, odstraňte skupinu prostředků pro trvalé odstranění všechno, co v ní, včetně služeb a uložený obsah, který jste vytvořili pro účely tohoto kurzu. Na portálu najdete název skupiny prostředků na stránce Přehled jednotlivých služeb.

## <a name="next-steps"></a>Další postup

Pokud si chcete kanál přizpůsobit nebo rozšířit, můžete použít vlastní dovednosti. Když si vytvoříte vlastní dovednost, kterou pak přidáte do sady dovedností, budete moct používat analýzu textu a obrazu, kterou si sami napíšete.

> [!div class="nextstepaction"]
> [Příklad: vytvoření vlastní dovednosti](cognitive-search-create-custom-skill-example.md)
