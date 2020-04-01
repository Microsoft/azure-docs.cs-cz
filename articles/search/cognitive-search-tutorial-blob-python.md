---
title: 'Kurz: Python a AI přes Azure objekty BLOB'
titleSuffix: Azure Cognitive Search
description: Krokování příklad extrakce textu a zpracování přirozeného jazyka přes obsah v úložišti objektů Blob pomocí poznámkového bloku Jupyter Python a Azure Cognitive Search REST API.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: e7708b0043b7f5baf2c12e813306595cc358a01d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78194050"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Kurz: Ke generování prohledávatelného obsahu z objektů BLOB Azure použijte Python a AI.

Pokud máte nestrukturovaný text nebo image v úložišti objektů Blob Azure, [kanál obohacení AI](cognitive-search-concept-intro.md) můžete extrahovat informace a vytvořit nový obsah, který je užitečný pro fulltextové vyhledávání nebo scénáře dolování znalostí. Přestože kanál může zpracovávat obrázky, tento kurz pythonu se zaměřuje na text, použití detekce jazyka a zpracování přirozeného jazyka k vytvoření nových polí, která můžete využít v dotazech, omezujících okolností a filtrech.

Tento kurz používá Python a [hledání REST API](https://docs.microsoft.com/rest/api/searchservice/) k provádění následujících úkolů:

> [!div class="checklist"]
> * Začněte s celými dokumenty (nestrukturovaným textem), jako jsou PDF, HTML, DOCX a PPTX v úložišti objektů Blob Azure.
> * Definujte kanál, který extrahuje text, detekuje jazyk, rozpozná entity a detekuje klíčové fráze.
> * Definujte index pro uložení výstupu (nezpracovaný obsah plus dvojice název-hodnota generovaný kanálem).
> * Spusťte kanál ke spuštění transformace a analýzy a vytvořit a načíst index.
> * Prozkoumejte výsledky pomocí fulltextového vyhledávání a bohaté syntaxe dotazu.

Pokud nemáte předplatné Azure, otevřete si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Anakonda 3,7](https://www.anaconda.com/distribution/#download-section)
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

## <a name="2---start-a-notebook"></a>2 - Spuštění poznámkového bloku

Vytvořte poznámkový blok podle následujících pokynů nebo si stáhněte hotový poznámkový blok z [repo ukázek Azure-Search-python.](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)

Pomocí aplikace Anaconda Navigator můžete spustit poznámkový blok Jupyter a vytvořit nový poznámkový blok Pythonu 3.

V poznámkovém bloku spusťte tento skript a načtěte knihovny používané pro práci s JSON a formulování požadavků HTTP.

```python
import json
import requests
from pprint import pprint
```

Ve stejném poznámkovém bloku definujte názvy zdroje dat, indexu, indexeru a sady dovedností. Spusťte tento skript a nastavte názvy pro tento kurz.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

V následujícím skriptu nahraďte zástupné symboly vyhledávací služby (VAŠE-SEARCH-SERVICE-NAME) a klíče rozhraní API pro správu (VAŠE-ADMIN-API-KEY) a spusťte jej a nastavte koncový bod vyhledávací služby.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="3---create-the-pipeline"></a>3 - Vytvoření potrubí

V Azure Cognitive Search, Zpracování AI dochází během indexování (nebo přilnutí dat). Tato část návodu vytvoří čtyři objekty: zdroj dat, definice indexu, skillset, indexer. 

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

[Objekt zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source) poskytuje připojovací řetězec ke kontejneru objektů Blob obsahujícímu soubory.

V následujícím skriptu nahraďte zástupný symbol YOUR-BLOB-RESOURCE-CONNECTION-STRING připojovacím řetězcem pro objekt blob, který jste vytvořili v předchozím kroku. Nahraďte zástupný text kontejneru. Potom spusťte skript a vytvořte zdroj dat s názvem `cogsrch-py-datasource`.

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
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

Požadavek by měl vrátit stavový kód 201 potvrzující úspěch.

Na webu Azure Portal na stránce řídicího panelu vyhledávací služby ověřte, že zdroj dat cogsrch-py se zobrazí v seznamu **Zdroje dat.** Kliknutím na **Aktualizovat** stránku aktualizujte.

![Dlaždice zdrojů dat na portálu](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Dlaždice zdrojů dat na portálu")

### <a name="step-2-create-a-skillset"></a>Krok 2: Vytvoření sady dovedností

V tomto kroku definujete sadu kroků obohacení, které se použijí na vaše data. Jednotlivým krokům rozšiřování se říká *dovednosti*, zatímco sada kroků rozšiřování je *sada dovedností*. Tento kurz používá [integrované kognitivní dovednosti](cognitive-search-predefined-skills.md) pro skillset:

+ [Rozpoznávání entit](cognitive-search-skill-entity-recognition.md) pro extrahování názvů organizací z obsahu v kontejneru objektů blob.

+ [Rozpoznávání jazyka](cognitive-search-skill-language-detection.md), které identifikuje jazyk obsahu

+ [Rozdělení textu](cognitive-search-skill-textsplit.md), které před zavoláním dovednosti extrakce klíčových frází rozdělí velký obsah do menších bloků Extrakce klíčových frází přijímá vstup složený z 50 000 znaků nebo méně. Některé ze zdrojových souborů je nutné rozdělit, aby se do tohoto limitu vešly.

+ [Extrakce klíčových frází](cognitive-search-skill-keyphrases.md), která získává hlavní klíčové fráze 

Spusťte následující skript a `cogsrch-py-skillset`vytvořte sadu dovedností nazvanou .

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

Pro každou stránku se použije dovednost extrakce klíčových frází. Nastavením kontextu `"document/pages/*"`na , spustíte tento obohacení pro každého člena pole dokumentu/stránek (pro každou stránku v dokumentu).

Pro obsah dokumentu se využijí jednotlivé dovednosti. Během zpracování Azure Cognitive Search rozlouskne každý dokument číst obsah z různých formátů souborů. Text nalezený ve zdrojovém `content` souboru je umístěn do pole, jeden pro každý dokument. Proto nastavte vstup `"/document/content"`jako .

Grafickou reprezentaci sady dovedností najdete níže.

![Pochopte sadu dovedností](media/cognitive-search-tutorial-blob/skillset.png "Pochopte sadu dovedností")

Výstupy lze mapovat na index, který se používá jako vstup do navazující dovednosti nebo obojí, jako je tomu v případě kódu jazyka. V indexu je kód jazyka užitečný při filtrování. Jako vstup se kód jazyka používá v dovednostech analýzy textu, čímž se jazykovým pravidlům poskytne informace o dělení slov.

Další informace o základních principech sady dovedností najdete v článku o [definování sady dovedností](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Krok 3: Vytvoření indexu

V této části definujete schéma indexu zadáním polí, která mají být zahrnuta do indexu s prohledávatelného, a nastavením atributů hledání pro každé pole. Pole mají typ a můžou přijímat argumenty, které určují, jak se pole používá (prohledávatelné, seřaditelné apod.). K vyhledání přesné shody s názvy polí ve zdroji není nutné, aby index tyto názvy polí obsahoval. V pozdějším kroku přidáte v indexeru mapování polí, pomocí kterých propojíte zdrojová a cílová pole. Pro tento krok definujte index pomocí konvencí pojmenování relevantních pro vaši vyhledávací aplikaci.

V tomto cvičení použijeme následující pole a jejich typy:

| Názvy polí: | id         | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| Typy polí: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Spusťte tento skript `cogsrch-py-index`a vytvořte index s názvem .

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

Další informace o definování indexu najdete v tématu [vytvoření indexu (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Krok 4: Vytvoření a spuštění indexeru

[Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) řídí potrubí. Tři komponenty, které jste dosud vytvořili (zdroj dat, skillset, index) jsou vstupy do indexeru. Vytvoření indexeru v Azure Cognitive Search je událost, která uvádí celý kanál do pohybu. 

Chcete-li tyto objekty spojit v indexeru, musíte definovat mapování polí.

+ PoleMapování jsou zpracovány před skillset, mapování zdrojových polí ze zdroje dat na cílová pole v indexu. Pokud jsou názvy a typy polí na obou koncích stejné, není vyžadováno žádné mapování.

+ OutputFieldMappings jsou zpracovány po skillset, odkazování sourceFieldNames, které neexistují, dokud dokument praskání nebo obohacení vytvoří. TargetFieldName je pole v indexu.

Kromě připojení vstupů k výstupům můžete také použít mapování polí ke sloučení datových struktur. Další informace naleznete v tématu [Mapování obohacených polí na index s prohledávatelným způsobem](cognitive-search-output-field-mapping.md).

Spusťte tento skript a `cogsrch-py-indexer`vytvořte indexer s názvem .

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

Požadavek by měl brzy vrátit stavový kód 201, zpracování však může trvat několik minut. Přestože je sada dat malá, analytické dovednosti, jako je například analýza obrazu, jsou výpočtově náročné a nějakou dobu trvat.

Můžete [sledovat stav indexeru](#check-indexer-status) k určení, kdy je indexer spuštěn nebo dokončen.

> [!TIP]
> Vytvoření indexeru vyvolá kanál. Pokud se objeví problém s přístupem k datům, mapování min. vstupy a výstupy nebo s pořadím operací, zobrazí se v této fázi. Chcete-li kanál znovu spustit se změnami kódu nebo skriptu, bude pravděpodobně nutné nejprve odstranit objekty. Další informace najdete v článku o [resetování a opětovném spuštění](#reset).

#### <a name="about-the-request-body"></a>O textu požadavku

Skript nastaví `"maxFailedItems"` na -1, což dá modulu indexování pokyn, aby během importování dat ignoroval chyby. To je užitečné, protože v ukázkovém zdroji dat je velmi málo dokumentů. Pro větší zdroje dat by tato hodnota byla větší než 0.

Navíc si všimněte příkazu `"dataToExtract":"contentAndMetadata"` v parametrech konfigurace. Tento příkaz říká indexerextrahovat obsah z různých formátů souborů a metadata vztahující se ke každému souboru.

Když se extrahuje obsah, můžete nastavit `imageAction`, aby se z obrázků nalezených ve zdroji dat extrahoval text. Konfigurace `"imageAction":"generateNormalizedImages"` v kombinaci s dovedností OCR a dovedností sloučení textu říká indexeru, aby extrahoval text z obrázků (například slovo "stop" ze znaménko stop provozu) a vkládal jej jako součást pole obsahu. Toto chování se vztahuje jak na obrazy vložené do dokumentů (představte si obraz uvnitř PDF), tak pro obrazy nalezené ve zdroji dat, například v souboru JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Sledování indexování

Až se indexer nadefinuje, automaticky se spustí, až se odešle požadavek. Podle kognitivních dovedností, které jste definovali, může indexování trvat déle, než jste čekali. Chcete-li zjistit, zda je zpracování indexeru dokončeno, spusťte následující skript.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

V odpovědi sledujte hodnoty "lastResult" pro jeho hodnoty "status" a "endTime". Pravidelně spouštějte skript a zkontrolujte stav. Po dokončení indexeru bude stav nastaven na "úspěch", bude zadán "endTime" a odpověď bude obsahovat všechny chyby a upozornění, ke kterým došlo během obohacení.

![Indexer je vytvořen](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Indexer je vytvořen")

Pro určité kombinace zdrojových souborů a dovedností jsou upozornění běžná a ne vždy představují problém. Mnoho varování je neškodné. Pokud například indexujete soubor JPEG, který nemá text, zobrazí se na tomto snímku obrazovky upozornění.

![Příklad upozornění indexeru](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Příklad upozornění indexeru")

## <a name="5---search"></a>5 - Hledání

Jakmile se indexování dokončí, spusťte dotazy, které vrátí obsah jednotlivých polí. Ve výchozím nastavení Azure Cognitive Search vrátí prvních 50 nejlepších výsledků. Ukázková data jsou malá, takže výchozí nastavení stačí. Až ale budete pracovat s většími sadami dat, budete možná potřebovat přidat do řetězce dotazu nějaké parametry, aby se výsledků vrátilo více. Pokyny najdete v tématu [Jak zobrazit výsledky stránky v Azure Cognitive Search](search-pagination-page-layout.md).

Jako krok ověření získáte definici indexu zobrazující všechna pole.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Výsledky by měly vypadat podobně jako v následujícím příkladu. Snímek obrazovky zobrazuje pouze část odpovědi.

![Index dotazu pro všechna pole](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Dotaz na index pro všechna pole")

Výstupem je schéma indexu s názvem, typem a atributy všech jednotlivých polí.

Pošlete druhý dotaz pro `"*"`, aby se vrátil všechen obsah jednoho pole, třeba `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Výsledky by měly vypadat podobně jako v následujícím příkladu. Snímek obrazovky zobrazuje pouze část odpovědi.

![Index dotazu pro obsah organizací](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Dotaz na index pro vrácení obsahu organizací")

Tento postup opakujte pro další pole: obsah, jazykKód, klíčové fráze a organizace v tomto cvičení. Prostřednictvím `$select` můžete pomocí seznamu hodnot oddělených čárkami vrátit více než jedno pole.

V závislosti na složitosti a délce řetězce dotazu můžete použít operace GET nebo POST. Další informace najdete v článku o [dotazování pomocí rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

V raných experimentálních fázích vývoje je nejpraktičtějším přístupem pro iteraci návrhu odstranění objektů z Azure Cognitive Search a povolení jejich opětovného sestavení kódu. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Portál můžete použít k odstranění indexů, indexerů, zdrojů dat a skillsets. Při odstranění indexeru můžete volitelně selektivně odstranit index, skillset a zdroj dat současně.

![Odstranění vyhledávacích objektů](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Odstranění vyhledávacích objektů na portálu")

Můžete je také odstranit pomocí skriptu. Následující skript ukazuje, jak odstranit skillset. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Při úspěšném odstranění se vrátí kód stavu 204.

## <a name="takeaways"></a>Shrnutí

Tento kurz ukazuje základní postup sestavení kanálu pro rozšířené indexování, při kterém se vytvářejí součásti: zdroj dat, sada dovedností, index a indexer.

Byly zavedeny [vestavěné dovednosti](cognitive-search-predefined-skills.md) spolu s definicemi dovedností a způsobem, jak spojit dovednosti prostřednictvím vstupů a výstupů. Také jste `outputFieldMappings` zjistili, že v definici indexeru je vyžadovánpro směrování obohacené hodnoty z kanálu do indexu prohledávatelné na Azure Cognitive Search služby.

Nakonec jste se naučili testovat výsledky a resetovat systém pro další iterace. Zjistili jste, že zasílání dotazů na index vrací výstup vytvořený kanálem rozšířeného indexování. V této verzi existuje mechanismus, jak si zobrazit vnitřní konstrukce (rozšířené dokumenty, které systém vytvořil). Také jste se dozvěděli, jak zkontrolovat stav indexeru a jaké objekty musí být odstraněny před opětovným spuštěním kanálu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Při práci ve vlastním předplatném je na konci projektu vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu Všechny prostředky nebo skupiny prostředků v levém navigačním podokně.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámeni se všemi objekty v kanálu obohacení AI, podívejme se blíže na definice dovedností a individuální dovednosti.

> [!div class="nextstepaction"]
> [Jak vytvořit sadu dovedností](cognitive-search-defining-skillset.md)
