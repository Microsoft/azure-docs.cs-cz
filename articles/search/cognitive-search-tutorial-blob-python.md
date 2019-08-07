---
title: 'Kurz Pythonu: Volání Cognitive Services v kanálu rozšíření AI – Azure Search'
description: Projděte si příklad extrakce dat, přirozeného jazyka a zpracování AI v Azure Search pomocí poznámkového bloku Pythonu Jupyter. Extrahovaná data jsou indexována a snadno se k nim přistupovaly pomocí dotazu.
manager: cgronlun
author: LisaLeib
services: search
ms.service: search
ms.subservice: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-lilei
ms.openlocfilehash: af355ec353e12c30723597927883cb23dfa31e71
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840919"
---
# <a name="python-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>Kurz Pythonu: Volání rozhraní API služeb Cognitive Services v kanálu indexování Azure Search

V tomto kurzu se naučíte, jak v Azure Search naprogramovat rozšiřování dat pomocí *kognitivních dovedností*. Dovednosti jsou zajištěny při zpracování přirozeného jazyka (NLP) a možností analýzy obrázků v Cognitive Services. Prostřednictvím složení a konfigurace dovednosti můžete extrahovat text a text reprezentace obrázku nebo naskenovaného souboru dokumentu. Můžete také zjistit jazyk, entity, klíčové fráze a další. Výsledkem je bohatě doplněný obsah v Azure Search indexu vytvořený s rozšířeními AI v kanálu indexování. 

V tomto kurzu pomocí Pythonu provedete následující úlohy:

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

+ [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section), který poskytuje Poznámkový blok Python 3. x a Jupyter.

+ [Ukázková data](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) se skládají z malých souborových sad různých typů. 

+ [Vytvořte službu Azure Search](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento kurz můžete použít bezplatnou službu.

## <a name="get-a-key-and-url"></a>Získat klíč a adresu URL

Abyste mohli komunikovat se službou Azure Search, budete potřebovat adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. Přihlaste se [k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení** > **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

![Získání koncového bodu http a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu http a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří vztah důvěryhodnosti na základě jednotlivých požadavků mezi aplikací odesílající požadavek a službu, která ho zpracovává.

## <a name="prepare-sample-data"></a>Příprava ukázkových dat

Kanál pro rozšiřování načítá informace ze zdrojů dat Azure. Zdrojová data musí pocházet z podporovaného typu zdroje dat [indexeru Azure Search](search-indexer-overview.md). Pro vyhledávání rozpoznávání se nepodporuje Azure Table Storage. Při tomto cvičení použijeme službu Blob Storage, na které ukážeme několik typů obsahu.

1. Přihlaste se [k Azure Portal](https://portal.azure.com), přejděte k účtu úložiště Azure, klikněte na **objekty blob**a pak klikněte na **+ kontejner**.

1. [Vytvořte kontejner objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) , který bude obsahovat vzorová data. Úroveň veřejného přístupu můžete nastavit na libovolnou z jeho platných hodnot.

1. Po vytvoření kontejneru ho otevřete a na panelu příkazů vyberte **nahrát** a nahrajte ukázkové soubory, které jste stáhli v předchozím kroku.

   ![Zdrojové soubory ve službě Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Až se ukázkové soubory nahrají, získejte název kontejneru a připojovací řetězec vaší služby Blob Storage. Najdete je, když přejdete na účet úložiště na portálu Azure Portal. Klikněte na **přístupové klíče**a zkopírujte pole **připojovací řetězec** .

Připojovací řetězec bude mít tento formát:`DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Ponechejte připojovací řetězec užitečný. Budete ho potřebovat v budoucím kroku.

Existují i jiné způsoby, jak zadat připojovací řetězec, například poskytnutím sdíleného přístupového podpisu. Další informace o přihlašovacích údajích ke zdroji dat najdete v článku o [indexování služby Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="create-a-jupyter-notebook"></a>Vytvoření poznámkového bloku Jupyter

> [!Note]
> V tomto článku se dozvíte, jak vytvořit zdroj dat, index, indexer a dovednosti pomocí řady skriptů Pythonu. Pokud si chcete stáhnout kompletní příklad poznámkového bloku, přečtěte si [úložiště ukázek Azure-Search-Python-Samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment-Jupyter-Notebook).

Pomocí Anaconda navigátoru můžete spustit Jupyter Notebook a vytvořit nový notebook Python 3.

## <a name="connect-to-azure-search"></a>Připojení k Azure Search

Ve svém poznámkovém bloku spuštěním tohoto skriptu načtěte knihovny používané pro práci s JSON a formulujte požadavky HTTP.

```python
import json
import requests
from pprint import pprint
```

Dále definujte názvy pro zdroj dat, index, indexer a dovednosti. Spuštěním tohoto skriptu nastavte názvy pro tento kurz.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

> [!Tip]
> Na bezplatné služby jste omezeni na tři indexy, indexery a zdroje dat. V tomto kurzu se vytváří od každého jeden. Než budete pokračovat, ujistěte se, že máte místo pro vytváření nových objektů.

V následujícím skriptu Nahraďte zástupné symboly pro vaši vyhledávací službu (název služby-SEARCH-SERVICE-NAME) a klíč rozhraní API pro správu (správce-klíč rozhraní API-KEY) a pak ho spusťte pro nastavení koncového bodu služby Search.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="create-a-data-source"></a>Vytvoření zdroje dat

Teď, když už máte připravené služby a zdrojové soubory, můžete začít sestavovat součásti kanálu indexování. Začněte s objektem zdroje dat, který instruuje Azure Search jak načíst externí zdrojová data.

V následujícím skriptu nahraďte zástupný symbol, a to pomocí připojovacího řetězce pro objekt blob, který jste vytvořili v předchozím kroku. Potom spuštěním skriptu vytvořte zdroj dat s názvem `cogsrch-py-datasource`.

```python
# Create a data source
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
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

Požadavek by měl vrátit stavový kód 201 potvrzující úspěch.

V Azure Portal na stránce řídicí panel služby vyhledávání ověřte, že se v seznamu **zdroje dat** zobrazí položka cogsrch-py-DataSource. Stránku můžete aktualizovat kliknutím na **aktualizovat** .

![Dlaždice Zdroje dat na portálu](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Dlaždice Zdroje dat na portálu")

## <a name="create-a-skillset"></a>Vytvoření sady dovedností

V tomto kroku definujete sadu kroků obohacení, které se použijí na vaše data. Jednotlivým krokům rozšiřování se říká *dovednosti*, zatímco sada kroků rozšiřování je *sada dovedností*. V tomto kurzu se používá integrovaný program pro [rozpoznávání hlasu](cognitive-search-predefined-skills.md) pro dovednosti:

+ [Rozpoznávání jazyka](cognitive-search-skill-language-detection.md), které identifikuje jazyk obsahu

+ [Rozdělení textu](cognitive-search-skill-textsplit.md), které před zavoláním dovednosti extrakce klíčových frází rozdělí velký obsah do menších bloků Extrakce klíčových frází přijímá vstup složený z 50 000 znaků nebo méně. Některé ze zdrojových souborů je nutné rozdělit, aby se do tohoto limitu vešly.

+ [Rozpoznávání entit](cognitive-search-skill-entity-recognition.md) pro extrahování názvů organizací z obsahu v kontejneru objektů BLOB.

+ [Extrakce klíčových frází](cognitive-search-skill-keyphrases.md), která získává hlavní klíčové fráze 

### <a name="python-script"></a>Skript Pythonu
Spuštěním následujícího skriptu vytvořte dovednosti s názvem `cogsrch-py-skillset`.

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
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
            "textSplitMode": "pages",
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
                    "name": "languageCode", "source": "/document/languageCode"
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

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

Požadavek by měl vrátit stavový kód 201 potvrzující úspěch.

Pro každou stránku se použije dovednost pro extrakci klíčových frází. Nastavením kontextu na `"document/pages/*"`je spuštění tohoto rozšíření pro každého člena pole dokumentu/stránky (pro každou stránku v dokumentu).

Pro obsah dokumentu se využijí jednotlivé dovednosti. Během zpracování služba Azure Search otevře každý dokument a přečte obsah z různých formátů souborů. Text, který se nachází ve zdrojovém souboru, `content` je umístěn do pole, jeden pro každý dokument. Proto nastavte vstup jako `"/document/content"`.

Grafickou reprezentaci sady dovedností najdete níže.

![Ilustrace sady dovedností](media/cognitive-search-tutorial-blob/skillset.png "Ilustrace sady dovedností")

Výstupy mohou být mapovány na index, který slouží jako vstup pro podřízenou dovednost, nebo obojí, stejně jako u kódu jazyka. V indexu je kód jazyka užitečný při filtrování. Jako vstup se kód jazyka používá v dovednostech analýzy textu, čímž se jazykovým pravidlům poskytne informace o dělení slov.

Další informace o základních principech sady dovedností najdete v článku o [definování sady dovedností](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Vytvoření indexu

V této části definujete schéma indexu zadáním polí, která mají být zahrnuta do prohledávatelný index, a nastavením atributů hledání pro každé pole. Pole mají typ a můžou přijímat argumenty, které určují, jak se pole používá (prohledávatelné, seřaditelné apod.). K vyhledání přesné shody s názvy polí ve zdroji není nutné, aby index tyto názvy polí obsahoval. V pozdějším kroku přidáte v indexeru mapování polí, pomocí kterých propojíte zdrojová a cílová pole. Pro tento krok definujte index pomocí konvencí pojmenování relevantních pro vaši vyhledávací aplikaci.

V tomto cvičení použijeme následující pole a jejich typy:

| Názvy polí: | id         | obsah   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| Typy polí: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Spusťte tento skript k vytvoření indexu s názvem `cogsrch-py-index`.

```python
# Create an index
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

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

Požadavek by měl vrátit stavový kód 201 potvrzující úspěch.

Další informace o definování indexu najdete v článku o [vytvoření indexu (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Vytvoření indexeru, mapování polí a spouštění transformací

Zatím jste vytvořili zdroj dat, dovednosti a index. Tyto tři komponenty se stanou součástí [indexeru](search-indexer-overview.md), který jednotlivé části sestaví do jediné operace s více fázemi. Chcete-li tyto objekty spojit dohromady v indexeru, je nutné definovat mapování polí.

+ FieldMappings se zpracovávají před dovednosti a mapuje zdrojová pole ze zdroje dat na cílová pole v indexu. Pokud jsou názvy polí a typy na obou koncích stejné, není nutné žádné mapování.

+ OutputFieldMappings se zpracovávají po dovednosti a odkazují na sourceFieldNames, které neexistují, dokud je nevytvoří dokument pro vyhodnocování nebo rozšíření. TargetFieldName je pole v indexu.

Kromě zapojení vstupů do výstupů můžete také použít mapování polí pro sloučení datových struktur. Další informace najdete v tématu [mapování obohacených polí na index s možností prohledávání](cognitive-search-output-field-mapping.md).

Spuštěním tohoto skriptu vytvořte indexer s názvem `cogsrch-py-indexer`.

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

Požadavek by měl rychle vrátit stavový kód 201, ale zpracování může trvat několik minut. I když je datová sada malá, analytické dovednosti, jako je například analýza obrázků, jsou výpočty náročné na výpočetní výkon a nějakou dobu trvat.

Pomocí skriptu pro [kontrolu stavu indexeru](#check-indexer-status) v další části určete, kdy se proces indexeru dokončí.

> [!TIP]
> Vytvoření indexeru vyvolá kanál. Pokud dojde k potížím při přístupu k datům, mapování vstupů a výstupů nebo v pořadí operací, zobrazí se v této fázi. Chcete-li znovu spustit kanál se změnami kódu nebo skriptu, bude pravděpodobně nutné nejprve odstranit objekty. Další informace najdete v článku o [resetování a opětovném spuštění](#reset).

#### <a name="explore-the-request-body"></a>Průzkum textu požadavku

Skript nastaví `"maxFailedItems"` na -1, což dá modulu indexování pokyn, aby během importování dat ignoroval chyby. To je užitečné, protože v ukázkovém zdroji dat je velmi málo dokumentů. Pro větší zdroje dat by tato hodnota byla větší než 0.

Navíc si všimněte příkazu `"dataToExtract":"contentAndMetadata"` v parametrech konfigurace. Tento příkaz dává indexeru pokyn, aby extrakci obsahu z různých formátů souborů a metadat souvisejících s každým souborem.

Když se extrahuje obsah, můžete nastavit `imageAction`, aby se z obrázků nalezených ve zdroji dat extrahoval text. V `"imageAction":"generateNormalizedImages"` kombinaci s dovedností optického textu a dovednosti pro sloučení textu říká indexeru, aby vyextrahovali text z obrázků (například slovo "Stop" z znaku zastavení provozu) a vloží ho jako součást pole Content. Toto chování platí pro obrázky vložené do dokumentů (můžete si představit obrázek v PDF) a obrázky, které se nacházejí ve zdroji dat, například soubor. JPG.

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>Kontrola stavu indexeru

Až se indexer nadefinuje, automaticky se spustí, až se odešle požadavek. Podle kognitivních dovedností, které jste definovali, může indexování trvat déle, než jste čekali. Chcete-li zjistit, zda bylo zpracování indexeru dokončeno, spusťte následující skript.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

V odpovědi Sledujte "lastResult" pro hodnoty "stav" a "čas". Pravidelně spouštějte skript pro kontrolu stavu. Až se indexer dokončí, stav se nastaví na "úspěch", zadá se "Čas_ukončení" a odpověď bude obsahovat všechny chyby a upozornění, ke kterým došlo během obohacování.

![Indexer je vytvořený] . (./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Indexer je vytvořený") .

Pro určité kombinace zdrojových souborů a dovedností jsou upozornění běžná a ne vždy představují problém. V tomto kurzu jsou upozornění neškodná. Například jeden ze souborů JPEG, které nemají text, zobrazí upozornění na tomto snímku obrazovky.

![Příklad upozornění indexeru](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Příklad upozornění indexeru")

## <a name="query-your-index"></a>Dotázání indexu

Jakmile se indexování dokončí, spusťte dotazy, které vrátí obsah jednotlivých polí. Standardně služba Azure Search vrací prvních 50 výsledků. Ukázková data jsou malá, takže výchozí nastavení stačí. Až ale budete pracovat s většími sadami dat, budete možná potřebovat přidat do řetězce dotazu nějaké parametry, aby se výsledků vrátilo více. Pokyny najdete v článku [Jak v Azure Search stránkovat výsledky hledání](search-pagination-page-layout.md).

Pro ověření pošlete indexu dotaz na všechna pole.

```python
# Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Výsledky by měly vypadat podobně jako v následujícím příkladu. Snímek obrazovky zobrazuje pouze část odpovědi.

![Index dotazu pro všechna pole](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Dotazování indexu pro všechna pole")

Výstupem je schéma indexu s názvem, typem a atributy všech jednotlivých polí.

Pošlete druhý dotaz pro `"*"`, aby se vrátil všechen obsah jednoho pole, třeba `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Výsledky by měly vypadat podobně jako v následujícím příkladu. Snímek obrazovky zobrazuje pouze část odpovědi.

![Index dotazu pro obsah organizací](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Dotazování indexu, který vrátí obsah organizací")

Opakujte pro další pole: obsah, languageCode, klíčová fráze a organizace v tomto cvičení. Prostřednictvím `$select` můžete pomocí seznamu hodnot oddělených čárkami vrátit více než jedno pole.

V závislosti na složitosti a délce řetězce dotazu můžete použít operace GET nebo POST. Další informace najdete v článku o [dotazování pomocí rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).
it <a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

V raných experimentálních fázích vývoje kanálu je při iterování návrhu nejpraktičtější, když se ze služby Azure Search odstraní objekty a umožní se kódu, aby je znovu vytvořil. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Pokud chcete dokumenty znovu indexovat s novými definicemi:

1. Odstraňte index, aby se odebrala trvalá data. Odstraňte indexer a znovu ho ve službě vytvořte.
2. Upravte definice dovednosti a index.
3. Znovu ve službě vytvořte index a indexer, abyste mohli spustit kanál.

Portál můžete použít k odstranění indexů, indexerů a dovednosti. Když indexer odstraníte, můžete také selektivně odstranit index, dovednosti a zdroj dat...

![Odstranit objekty hledání](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Odstranění objektů hledání na portálu")

Můžete je také odstranit pomocí skriptu. Následující skript odstraní dovednosti, který jsme vytvořili. Požadavek na odstranění indexu, indexeru a zdroje dat můžete snadno upravit.

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Až se váš kód bude blížit dokončení, možná budete chtít zdokonalit strategii opětovného sestavování. Další informace najdete v článku o tom, [jak znovu sestavit index](search-howto-reindex.md).

## <a name="takeaways"></a>Shrnutí

Tento kurz ukazuje základní postup sestavení kanálu pro rozšířené indexování, při kterém se vytvářejí součásti: zdroj dat, sada dovedností, index a indexer.

Byly představeny [předdefinované dovednosti](cognitive-search-predefined-skills.md) společně s definicemi dovednosti a způsobem, jak zřetězit dovednosti prostřednictvím vstupů a výstupů. Naučili jste se, že `outputFieldMappings` v definici indexu umožňuje směrovat rozšířené hodnoty z kanálu do prohledávatelného indexu ve službě Azure Search.

Nakonec jste zjistili, jak testovat výsledky a obnovit systém pro další iterace. Zjistili jste, že zasílání dotazů na index vrací výstup vytvořený kanálem rozšířeného indexování. V této verzi existuje mechanismus, jak si zobrazit vnitřní konstrukce (rozšířené dokumenty, které systém vytvořil). Zjistili jste také, jak kontrolovat stav indexeru a jaké objekty je nutné před opakovaným spuštěním kanálu odstranit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejší způsob, jak po kurzu vše vyčistit, je odstranit skupinu prostředků, která obsahuje službu Azure Search a službu Azure Blob. Za předpokladu, že obě služby umístíte do stejné skupiny, odstraňte skupinu prostředků, abyste trvale odstranili vše, včetně služeb a veškerého uloženého obsahu, který jste pro tento kurz vytvořili. Na portálu najdete název skupiny prostředků na stránce Přehled jednotlivých služeb.

## <a name="next-steps"></a>Další postup

Pokud si chcete kanál přizpůsobit nebo rozšířit, můžete použít vlastní dovednosti. Když si vytvoříte vlastní dovednost, kterou pak přidáte do sady dovedností, budete moct používat analýzu textu a obrazu, kterou si sami napíšete.

> [!div class="nextstepaction"]
> [Příklad: Vytvoření vlastní dovednosti pro hledání vnímání](cognitive-search-create-custom-skill-example.md)
