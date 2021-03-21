---
title: 'Kurz: Python a AI přes objekty blob Azure'
titleSuffix: Azure Cognitive Search
description: Projděte si příklad extrakce textu a zpracování přirozeného jazyka přes obsah v úložišti objektů BLOB pomocí poznámkového bloku Pythonu Jupyter a rozhraní REST API pro Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 09/25/2020
ms.custom: devx-track-python
ms.openlocfilehash: ea1cc022cbea7dbf3d1fa12cb83cfe3084b28560
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92788077"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Kurz: použití Pythonu a AI k vygenerování prohledávatelných obsahu z objektů blob Azure

Pokud máte v úložišti objektů BLOB v Azure nestrukturovaný text nebo obrázky, [kanál pro rozšíření AI](cognitive-search-concept-intro.md) může extrahovat informace a vytvořit nový obsah, který je vhodný pro scénáře fulltextového vyhledávání nebo dolování ve znalostní bázi. I když může kanál zpracovat image, tento kurz Pythonu se zaměřuje na text, použití detekce jazyka a zpracování přirozeného jazyka pro vytváření nových polí, která můžete využít v dotazech, omezujících objektech a filtrech.

Tento kurz používá Python a [rozhraní REST API pro vyhledávání](/rest/api/searchservice/) k provádění následujících úloh:

> [!div class="checklist"]
> * Začněte s celými dokumenty (nestrukturovaný text), například PDF, HTML, DOCX a PPTX v úložišti objektů BLOB v Azure.
> * Definujte kanál, který extrahuje text, detekuje jazyk, rozpoznává entity a detekuje klíčové fráze.
> * Definujte index pro uložení výstupu (nezpracovaný obsah, plus páry název-hodnota vygenerované kanály).
> * Spusťte kanál, abyste mohli začít transformovat a analyzovat a vytvořit a načíst index.
> * Prozkoumejte výsledky pomocí fulltextového vyhledávání a bohatou syntaxí dotazu.

Pokud ještě nemáte předplatné Azure, otevřete si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3,7](https://www.anaconda.com/distribution/#download-section)
+ [Vytvoření](search-create-service-portal.md) nebo [vyhledání existující vyhledávací služby](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Pro tento kurz můžete použít bezplatnou službu. Bezplatná vyhledávací služba omezuje tři indexy, tři indexery a tři zdroje dat. V tomto kurzu se vytváří od každého jeden. Než začnete, ujistěte se, že máte ve své službě místo pro přijímání nových prostředků.

## <a name="download-files"></a>Stažení souborů

1. Otevřete tuto [složku OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) a v levém horním rohu klikněte na **Stáhnout** a zkopírujte soubory do počítače. 

1. Klikněte pravým tlačítkem na soubor zip a vyberte **Extrahovat vše**. Existuje 14 souborů různých typů. Pro toto cvičení budete používat 7.

## <a name="1---create-services"></a>1. vytvoření služeb

V tomto kurzu se používá Azure Kognitivní hledání pro indexování a dotazy, Cognitive Services v back-endu pro rozšíření AI a Azure Blob Storage k poskytnutí dat. Tento kurz zůstává v rámci bezplatného přidělení 20 transakcí na indexer za den v Cognitive Services, takže je potřeba, abyste vytvořili jenom služby pro vyhledávání a úložiště.

Pokud je to možné, vytvořte oba ve stejné oblasti a skupině prostředků pro možnost blízkost a spravovatelnost. V praxi může být váš účet Azure Storage v jakékoli oblasti.

### <a name="start-with-azure-storage"></a>Začínáme s Azure Storage

1. [Přihlaste se k Azure Portal](https://portal.azure.com/) a klikněte na **+ vytvořit prostředek**.

1. Vyhledejte *účet úložiště* a vyberte nabídku účtu úložiště od Microsoftu.

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="Vytvořit účet úložiště" border="false":::

1. Na kartě základy jsou vyžadovány následující položky. Přijměte výchozí hodnoty pro všechno ostatní.

   + **Skupina prostředků**. Vyberte existující jednu nebo vytvořte novou, ale použijte stejnou skupinu pro všechny služby, abyste je mohli souhrnně spravovat.

   + **Název účtu úložiště:** Pokud se domníváte, že máte více prostředků stejného typu, použijte název k jednoznačnému odstranění podle typu a oblasti, například *blobstoragewestus*. 

   + **Umístění:** Pokud je to možné, vyberte stejné umístění, které se používá pro Azure Kognitivní hledání a Cognitive Services. Jediné místo má za vyrušení poplatky za šířku pásma.

   + **Druh účtu**. Vyberte výchozí *StorageV2 (obecné účely v2)*.

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** službu vytvořte.

1. Po vytvoření klikněte na **Přejít k prostředku** a otevřete stránku Přehled.

1. Klikněte na služba **BLOB** Service.

1. Kliknutím na **+ kontejner** vytvořte kontejner a pojmenujte ho *ozubeného kola-Search-demo*.

1. Vyberte *ozubeného kola-Search-demo* a potom kliknutím na **Odeslat** otevřete složku, kam jste uložili soubory ke stažení. Vyberte všechny soubory bez imagí. Měli byste mít 7 souborů. Kliknutím na tlačítko **OK** nahrajte.

   :::image type="content" source="media/cognitive-search-tutorial-blob/sample-files.png" alt-text="Nahrání ukázkových souborů" border="false":::

1. Než ponecháte Azure Storage, Získejte připojovací řetězec, abyste mohli formulovat připojení v Azure Kognitivní hledání. 

   1. Přejděte zpět na stránku Přehled vašeho účtu úložiště (jako příklad jsme použili *blobstragewestus* ). 
   
   1. V levém navigačním podokně vyberte **přístupové klíče** a zkopírujte jeden z připojovacích řetězců. 

   Připojovací řetězec je adresa URL podobná následujícímu příkladu:

      ```http
      DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Uložte připojovací řetězec do poznámkového bloku. Budete ho potřebovat později při nastavování připojení ke zdroji dat.

### <a name="cognitive-services"></a>Cognitive Services

Obohacení AI je zajištěno Cognitive Services, včetně Analýza textu a Počítačové zpracování obrazu pro zpracování přirozeného jazyka a obrazu. Pokud by vaším cílem bylo dokončit skutečný prototyp nebo projekt, měli byste v tomto okamžiku zřídit Cognitive Services (ve stejné oblasti jako Azure Kognitivní hledání), abyste ho mohli připojit k operacím indexování.

Vzhledem k tomu, že tento kurz používá jenom 7 transakcí, můžete přeskočit zřizování prostředků, protože Azure Kognitivní hledání se může připojit k Cognitive Services a 20 bezplatných transakcí na indexer. Bezplatné přidělení je dostatečné. Pro větší projekty Naplánujte zřizování Cognitive Services na úrovni průběžných plateb. Další informace najdete v tématu věnovaném [připojení Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Třetí součástí je Azure Kognitivní hledání, kterou můžete vytvořit na [portálu](search-create-service-portal.md). K dokončení tohoto průvodce můžete použít úroveň Free. 

Stejně jako u služby Azure Blob Storage si pro získání přístupového klíče chvíli počkejte. Když při zahájení strukturování požadavků začnete, budete muset zadat koncový bod a klíč rozhraní API pro správu, který se použije k ověření každého požadavku.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Získání klíčového rozhraní API pro správu a adresy URL pro Azure Kognitivní hledání

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte název vaší vyhledávací služby. Název služby můžete potvrdit zkontrolováním adresy URL koncového bodu. Pokud byla adresa URL koncového bodu `https://mydemo.search.windows.net` , název vaší služby by byl `mydemo` .

2. V části **Nastavení**  >  **klíče** Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   Získejte taky klíč dotazu. Osvědčeným postupem je vystavovat požadavky na dotazy s přístupem jen pro čtení.

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="Získání názvu služby a klíčů pro správu a dotazy" border="false":::

Všechny požadavky vyžadují klíč rozhraní API-Key v hlavičce všech požadavků odeslaných službě. Platný klíč vytváří vztah důvěryhodnosti na základě jednotlivých požadavků mezi aplikací odesílající požadavek a službu, která ho zpracovává.

## <a name="2---start-a-notebook"></a>2. spuštění poznámkového bloku

Poznámkový blok vytvoříte pomocí následujících pokynů nebo si stáhněte kompletní Poznámkový blok z [úložiště Azure-Search-Python-Samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment).

Pomocí Anaconda navigátoru můžete spustit Jupyter Notebook a vytvořit nový notebook Python 3.

Ve svém poznámkovém bloku spuštěním tohoto skriptu načtěte knihovny používané pro práci s JSON a formulujte požadavky HTTP.

```python
import json
import requests
from pprint import pprint
```

Ve stejném poznámkovém bloku definujte názvy pro zdroj dat, index, indexer a dovednosti. Spuštěním tohoto skriptu nastavte názvy pro tento kurz.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

V následujícím skriptu Nahraďte zástupné symboly pro vaši vyhledávací službu (název služby-SEARCH-SERVICE-NAME) a klíč rozhraní API pro správu (správce-klíč rozhraní API-KEY) a pak ho spusťte pro nastavení koncového bodu služby Search.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2020-06-30'
}
```

## <a name="3---create-the-pipeline"></a>3. vytvoření kanálu

Ve službě Azure Kognitivní hledání se při indexování (nebo ingestování dat) objevuje zpracování AI. Tato část průvodce provede vytvořením čtyř objektů: zdroj dat, definice indexu, dovednosti, indexer. 

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat

[Objekt zdroje dat](/rest/api/searchservice/create-data-source) poskytuje připojovací řetězec k kontejneru objektů blob, který obsahuje soubory.

V následujícím skriptu nahraďte zástupný symbol, a to pomocí připojovacího řetězce pro objekt blob, který jste vytvořili v předchozím kroku. Nahraďte zástupný text pro kontejner. Potom spuštěním skriptu vytvořte zdroj dat s názvem `cogsrch-py-datasource` .

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

V Azure Portal na stránce řídicí panel služby vyhledávání ověřte, že se v seznamu **zdroje dat** zobrazí položka cogsrch-py-DataSource. Stránku můžete aktualizovat kliknutím na **aktualizovat** .

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-data-source-tile.png" alt-text="Dlaždice zdroje dat na portálu" border="false":::

### <a name="step-2-create-a-skillset"></a>Krok 2: vytvoření dovednosti

V tomto kroku definujete sadu kroků obohacení, které se použijí na vaše data. Jednotlivým krokům rozšiřování se říká *dovednosti*, zatímco sada kroků rozšiřování je *sada dovedností*. V tomto kurzu se používá integrovaný program pro [rozpoznávání hlasu](cognitive-search-predefined-skills.md) pro dovednosti:

+ [Rozpoznávání entit](cognitive-search-skill-entity-recognition.md) pro extrahování názvů organizací z obsahu v kontejneru objektů BLOB.

+ [Rozpoznávání jazyka](cognitive-search-skill-language-detection.md), které identifikuje jazyk obsahu

+ [Rozdělení textu](cognitive-search-skill-textsplit.md), které před zavoláním dovednosti extrakce klíčových frází rozdělí velký obsah do menších bloků Extrakce klíčových frází přijímá vstup složený z 50 000 znaků nebo méně. Některé ze zdrojových souborů je nutné rozdělit, aby se do tohoto limitu vešly.

+ [Extrakce klíčových frází](cognitive-search-skill-keyphrases.md), která získává hlavní klíčové fráze 

Spuštěním následujícího skriptu vytvořte dovednosti s názvem `cogsrch-py-skillset` .

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
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", 
                    "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
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
                    "name": "text", 
                    "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", 
                    "source": "/document/languageCode"
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

Pro každou stránku se použije dovednost pro extrakci klíčových frází. Nastavením kontextu na je `"document/pages/*"` spuštění tohoto rozšíření pro každého člena pole dokumentu/stránky (pro každou stránku v dokumentu).

Pro obsah dokumentu se využijí jednotlivé dovednosti. Během zpracování Azure Kognitivní hledání napraskliní každý dokument, aby četl obsah z různých formátů souborů. Text, který se nachází ve zdrojovém souboru, je umístěn do `content` pole, jeden pro každý dokument. Proto nastavte vstup jako `"/document/content"` .

Grafickou reprezentaci sady dovedností najdete níže.

:::image type="content" source="media/cognitive-search-tutorial-blob/skillset.png" alt-text="Pochopení dovednosti" border="false":::

Výstupy mohou být mapovány na index, který slouží jako vstup pro podřízenou dovednost, nebo obojí, stejně jako u kódu jazyka. V indexu je kód jazyka užitečný při filtrování. Jako vstup se kód jazyka používá v dovednostech analýzy textu, čímž se jazykovým pravidlům poskytne informace o dělení slov.

Další informace o základních principech sady dovedností najdete v článku o [definování sady dovedností](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Krok 3: vytvoření indexu

V této části definujete schéma indexu zadáním polí, která mají být zahrnuta do prohledávatelný index, a nastavením atributů hledání pro každé pole. Pole mají typ a můžou přijímat argumenty, které určují, jak se pole používá (prohledávatelné, seřaditelné apod.). K vyhledání přesné shody s názvy polí ve zdroji není nutné, aby index tyto názvy polí obsahoval. V pozdějším kroku přidáte v indexeru mapování polí, pomocí kterých propojíte zdrojová a cílová pole. Pro tento krok definujte index pomocí konvencí pojmenování relevantních pro vaši vyhledávací aplikaci.

V tomto cvičení použijeme následující pole a jejich typy:

| Názvy polí: | ID         | obsah   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| Typy polí: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Spusťte tento skript k vytvoření indexu s názvem `cogsrch-py-index` .

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

Další informace o definování indexu najdete v tématu [vytvoření indexu (Azure Kognitivní hledání REST API)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Krok 4: vytvoření a spuštění indexeru

[Indexer](/rest/api/searchservice/create-indexer) vyřídí kanál. Tři komponenty, které jste doposud vytvořili (zdroj dat, dovednosti, index), jsou vstupy pro indexer. Vytvoření indexeru v Azure Kognitivní hledání je událost, která vloží celý kanál do pohybu. 

Chcete-li tyto objekty spojit dohromady v indexeru, je nutné definovat mapování polí.

+ Je `"fieldMappings"` zpracována před dovednosti a mapuje zdrojová pole ze zdroje dat na cílová pole v indexu. Pokud jsou názvy polí a typy na obou koncích stejné, není nutné žádné mapování.

+ Je `"outputFieldMappings"` zpracována po dovednosti, odkazování na `"sourceFieldNames"` to, které neexistují, dokud se dokument nevytvoří nebo ho nebude vytvářet. `"targetFieldName"`Je pole v indexu.

Kromě zapojení vstupů do výstupů můžete také použít mapování polí pro sloučení datových struktur. Další informace najdete v tématu [mapování obohacených polí na index s možností prohledávání](cognitive-search-output-field-mapping.md).

Spuštěním tohoto skriptu vytvořte indexer s názvem `cogsrch-py-indexer` .

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

Požadavek by měl brzy vrátit stavový kód 201, ale zpracování může trvat několik minut. I když je datová sada malá, analytické dovednosti, jako je například analýza obrázků, jsou výpočty náročné na výpočetní výkon a nějakou dobu trvat.

Můžete [monitorovat stav indexeru](#check-indexer-status) a zjistit, kdy indexer běží nebo je dokončený.

> [!TIP]
> Vytvoření indexeru vyvolá kanál. Pokud dojde k potížím při přístupu k datům, mapování vstupů a výstupů nebo v pořadí operací, zobrazí se v této fázi. Chcete-li znovu spustit kanál se změnami kódu nebo skriptu, bude pravděpodobně nutné nejprve odstranit objekty. Další informace najdete v článku o [resetování a opětovném spuštění](#reset).

#### <a name="about-the-request-body"></a>O textu žádosti

Skript nastaví `"maxFailedItems"` na -1, což dá modulu indexování pokyn, aby během importování dat ignoroval chyby. To je užitečné, protože v ukázkovém zdroji dat je velmi málo dokumentů. Pro větší zdroje dat by tato hodnota byla větší než 0.

Navíc si všimněte příkazu `"dataToExtract":"contentAndMetadata"` v parametrech konfigurace. Tento příkaz dává indexeru pokyn, aby extrakci obsahu z různých formátů souborů a metadat souvisejících s každým souborem.

Když se extrahuje obsah, můžete nastavit `imageAction`, aby se z obrázků nalezených ve zdroji dat extrahoval text. V `"imageAction":"generateNormalizedImages"` kombinaci s dovedností optického textu a dovednosti pro sloučení textu říká indexeru, aby vyextrahovali text z obrázků (například slovo "Stop" z znaku zastavení provozu) a vloží ho jako součást pole Content. Toto chování platí pro obrázky vložené do dokumentů (můžete si představit obrázek v PDF) a obrázky, které se nacházejí ve zdroji dat, například soubor. JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4. monitorování indexování

Až se indexer nadefinuje, automaticky se spustí, až se odešle požadavek. Podle kognitivních dovedností, které jste definovali, může indexování trvat déle, než jste čekali. Chcete-li zjistit, zda bylo zpracování indexeru dokončeno, spusťte následující skript.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

V odpovědi Sledujte `"lastResult"` `"status"` `"endTime"` hodnoty a. Pravidelně spouštějte skript pro kontrolu stavu. Až se indexer dokončí, stav se nastaví na "úspěch", zadá se "Čas_ukončení" a odpověď bude obsahovat všechny chyby a upozornění, ke kterým došlo během obohacování.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png" alt-text="Indexer je vytvořený." border="false":::

Pro určité kombinace zdrojových souborů a dovedností jsou upozornění běžná a ne vždy představují problém. Mnoho upozornění je neškodné. Pokud například nasadíte soubor JPEG, který nemá text, zobrazí se upozornění na tomto snímku obrazovky.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png" alt-text="Příklad upozornění indexeru" border="false":::

## <a name="5---search"></a>5 – hledání

Jakmile se indexování dokončí, spusťte dotazy, které vrátí obsah jednotlivých polí. Ve výchozím nastavení vrátí Azure Kognitivní hledání výsledky nejvyšší 50. Ukázková data jsou malá, takže výchozí nastavení stačí. Až ale budete pracovat s většími sadami dat, budete možná potřebovat přidat do řetězce dotazu nějaké parametry, aby se výsledků vrátilo více. Pokyny najdete v tématu [o výsledcích stránky v Azure kognitivní hledání](search-pagination-page-layout.md).

Jako ověřovací krok Získejte definici indexu zobrazující všechna pole.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Výsledky by měly vypadat podobně jako v následujícím příkladu. Snímek obrazovky zobrazuje pouze část odpovědi.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png" alt-text="Index dotazu pro všechna pole" border="false":::

Výstupem je schéma indexu s názvem, typem a atributy všech jednotlivých polí.

Pošlete druhý dotaz pro `"*"`, aby se vrátil všechen obsah jednoho pole, třeba `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Výsledky by měly vypadat podobně jako v následujícím příkladu. Snímek obrazovky zobrazuje pouze část odpovědi.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png" alt-text="Index dotazu pro obsah organizací" border="false":::

Opakujte pro další pole: `content` , `languageCode` , a `keyPhrases` `organizations` v tomto cvičení. Prostřednictvím `$select` můžete pomocí seznamu hodnot oddělených čárkami vrátit více než jedno pole.

V závislosti na složitosti a délce řetězce dotazu můžete použít operace GET nebo POST. Další informace najdete v článku o [dotazování pomocí rozhraní REST API](/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetování a opětovné spuštění

Ve fázích předčasného experimentu vývoje je nejužitečnějším přístupem k iteraci návrhu odstranění objektů z Azure Kognitivní hledání a umožnění kódu jejich opětovného sestavení. Názvy prostředků jsou jedinečné. Když se objekt odstraní, je možné ho znovu vytvořit se stejným názvem.

Portál můžete použít k odstranění indexů, indexerů, zdrojů dat a dovednosti. Když indexer odstraníte, můžete také selektivně odstranit index, dovednosti a zdroj dat...

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png" alt-text="Odstranění objektů hledání na portálu" border="false":::

Můžete je také odstranit pomocí skriptu. Následující skript ukazuje, jak odstranit dovednosti. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Při úspěšném odstranění se vrátí kód stavu 204.

## <a name="takeaways"></a>Shrnutí

Tento kurz ukazuje základní postup sestavení kanálu pro rozšířené indexování, při kterém se vytvářejí součásti: zdroj dat, sada dovedností, index a indexer.

Byly představeny [integrované dovednosti](cognitive-search-predefined-skills.md) společně s definicemi dovednosti a způsobem, jak zřetězit dovednosti prostřednictvím vstupů a výstupů. Zjistili jste taky, že `outputFieldMappings` v definici indexeru se vyžaduje směrování hodnot obohacených z kanálu do indexu s možností vyhledávání ve službě Azure kognitivní hledání.

Nakonec jste zjistili, jak testovat výsledky a obnovit systém pro další iterace. Zjistili jste, že zasílání dotazů na index vrací výstup vytvořený kanálem rozšířeného indexování. V této verzi existuje mechanismus, jak si zobrazit vnitřní konstrukce (rozšířené dokumenty, které systém vytvořil). Zjistili jste také, jak kontrolovat stav indexeru a jaké objekty je nutné před opakovaným spuštěním kanálu odstranit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete ve vlastním předplatném, je vhodné odebrat prostředky, které už nepotřebujete. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu všechny prostředky nebo skupiny prostředků v levém navigačním podokně.

## <a name="next-steps"></a>Další kroky

Teď, když jste obeznámení se všemi objekty v kanálu obohacení AI, se podíváme na dovednosti definice a jednotlivé dovednosti.

> [!div class="nextstepaction"]
> [Vytvoření dovednosti](cognitive-search-defining-skillset.md)