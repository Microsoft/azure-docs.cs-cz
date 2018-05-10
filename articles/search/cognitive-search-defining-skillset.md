---
title: Vytvoření skillset v kanálu kognitivní vyhledávání (Azure Search) | Microsoft Docs
description: Definování dat extrakce, přirozeného jazyka zpracování, nebo použijte image analysis postup zlepšit komunikaci oddělení a extrahovat strukturovaných informace z vašich dat pro ve službě Azure Search.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 3ab35cfd8ce5cf54a68473736fe05b78d26850de
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>Postup vytvoření skillset v kanálu obohacení

Kognitivní vyhledávání extrahuje a vylepšuje data tak, aby s možností vyhledávání ve službě Azure Search. Říkáme kroky extrakce a obohacení *kognitivní dovednosti*, kombinované do *skillset* odkazuje během indexování. Můžete použít skillset [předdefinované dovednosti](cognitive-search-predefined-skills.md) nebo vlastní dovednosti (najdete v části [příklad: vytvořit vlastní odborností](cognitive-search-create-custom-skill-example.md) Další informace).

V tomto článku zjistěte, jak vytvořit kanál obohacení pro dovedností, které chcete použít. Do služby Azure Search je připojen skillset [indexer](search-indexer-overview.md). Část návrhu kanálu, popsaná v tomto článku je vytváření skillset sám sebe. 

> [!NOTE]
> Další součástí návrhu kanálu je zadání indexeru, zahrnuté v [další krok](#next-step). Definici indexer obsahuje odkaz na skillset plus mapování polí použité pro připojování vstupu na výstup v cílový index.

Klíčové body:

+ Může mít pouze jeden skillset za indexer.
+ Skillset musí mít alespoň jeden znalostí.
+ Můžete vytvořit více dovednosti stejného typu (například varianty odborností analysis image), ale každý odborností může být použit pouze jednou v rámci stejné skillset.

## <a name="begin-with-the-end-in-mind"></a>Začínat konci v paměti

Doporučený krok počáteční je rozhodnutí, která data extrahovat z nezpracovaných dat a jak chcete tato data použít v řešení pro vyhledávání. Vytváření ilustraci kanálu celý obohacení můžete identifikovat potřebné kroky.

Předpokládejme, že máte zájem zpracování sadu finanční analytik komentáře. Pro každý soubor chcete extrahovat názvy společnosti a obecné postojích komentářů. Můžete také psát vlastní enricher, která používá službu vyhledávání Entity Bing na další informace o společnosti, jako je například jaké obchodních společnost pracuje ve. V podstatě chcete extrahovat informace, jako jsou následující, indexované pro každý dokument:

| záznam textu | Společnosti | Postojích | popisy společnosti |
|--------|-----|-----|-----|
|Ukázka záznamu| ["Microsoft", "LinkedIn"] | 0,99 | ["Microsoft Corporation je American mezinárodní technologie společnosti...", "LinkedIn je a a jejich zaměstnání orientovanými na firmu sociálních sítí..."]

Následující diagram znázorňuje hypotetický obohacení kanál:

![Kanál hypotetický obohacení](media/cognitive-search-defining-skillset/sample-skillset.png "hypotetický obohacení kanálu")


Jakmile budete mít správného představu o tom, co chcete v kanálu, můžete express skillset, který poskytuje tyto kroky. Skillset je funkčně vyjádřit v případě, že vaše definice indexer nahrajete do služby Azure Search. Další informace o tom, jak nahrát indexer najdete v tématu [indexer dokumentaci](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


V diagramu *hádání dokumentu toho* krok probíhá automaticky. V podstatě Azure Search umí otevřít soubory dobře známé a vytvoří *obsah* pole obsahující text extrahoval z každého dokumentu. Bílé polí jsou předdefinované enrichers a pole desítkovém "Vyhledat Entity Bing" představuje vlastní enricher, který vytvoříte. Jak je znázorněno, skillset obsahuje tři znalostí.

## <a name="skillset-definition-in-rest"></a>Definice Skillset v REST

Skillset je definován jako pole znalostí. Každou dovednosti definuje zdroj vstupy a výstupy vytváří název. Pomocí [vytvořit Skillset REST API](ref-create-skillset.md), můžete definovat skillset, která odpovídá předchozímu diagramu: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
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
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar",
      },
      "context": "/document/content/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/content/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Vytvoření skillset

Při vytváření skillset, můžete zadat popis aby dokumentace samoobslužné skillset. Popis je volitelný, ale užitečná pro udržování přehledu o jaké skillset. Protože skillset je dokument JSON, který neumožňuje komentáře, je nutné použít `description` pro tento element.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Další část v skillset je pole znalostí. Každou dovednosti si můžete představit jako primitivní obohacení. Každou dovednosti provede malé úlohu v obohacení kanálu. Každé z nich trvá vstup (nebo sadu vstupy) a vrátí některé výstupy. V dalších oddílech několik soustředit na určení znalosti předdefinované a vlastní řetězení dovednosti společně prostřednictvím vstupní a výstupní odkazy. Vstupy může pocházet ze zdroje dat nebo z další dovedností. Výstupy lze mapovat na pole v indexu vyhledávání nebo použít jako vstup pro příjem dat znalostí.

## <a name="add-predefined-skills"></a>Přidat předdefinované dovednosti

Podívejme se na první dovedností, což je předdefinovanou [s názvem entity rozpoznávání odborností](cognitive-search-skill-named-entity-recognition.md):

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
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
    }
```

* Má každý předdefinované odborností `odata.type`, `input`, a `output` vlastnosti. Vlastnosti specifické pro odborností poskytují další informace pro dovednosti. Pro rozpoznávání entity `categories` je jedna entita mezi pevnou sadu typy entit, které může rozpoznat pretrained modelu.

* Musí mít každý dovedností ```"context"```. Kontext představuje úroveň, kdy se probíhaly operace. Ve výše uvedené dovedností kontext není celý dokument, což znamená, že odborností rozpoznávání pojmenované entity je vyvolána jedenkrát za dokumentu. Výstupy také vytváří na této úrovni. Přesněji řečeno ```"organizations"``` jsou generovány jako člen skupiny ```"/document"```. V podřízené znalosti, můžete se podívat do to nově vytvořený informace jako ```"/document/organizations"```.  Pokud ```"context"``` pole není explicitně nastavena, je výchozí kontext dokumentu.

* Dovednosti má jeden vstup názvem "text", se sadou vstupní zdroj k ```"/document/content"```. Odborností (s názvem rozpoznávání entity) funguje na *obsah* pole pro každý dokument, což je standardní pole vytvořené indexeru objektů blob v Azure. 

* Dovednosti má jeden výstup názvem ```"organizations"```. Výstupy existovat pouze během zpracování. Zřetězit tento výstup vstupu podřízené odborností, odkazovat na výstup jako ```"/document/organizations"```.

* U konkrétního dokumentu, hodnota ```"/document/organizations"``` je pole organizací extrahovat z textu. Příklad:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Některé situace volání pro odkazování na každý element pole samostatně. Předpokládejme například, že chcete předat každý element ```"/document/organizations"``` samostatně na další dovedností (například vlastní Bing entity vyhledávání enricher). Přidáním hvězdičku k cestě, můžete odkazovat na každý element pole: ```"/document/organizations/*"``` 

Druhý dovednosti pro extrakci postojích následující stejné jako první enricher. Jak dlouho trvá ```"/document/content"``` jako vstup a vrátí pro každou instanci obsahu stanovení skóre postojích. Vzhledem k tomu, že jste nenastavili ```"context"``` pole explicitně, výstup (mySentiment) je nyní podřízená ```"/document"```.

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>Přidat vlastní dovedností

Odvolat strukturu vlastní enricher vyhledávání entity Bing:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar",
      }
      "context": "/document/content/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/content/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Tato definice je vlastní odborností, která volá webové rozhraní API v rámci procesu obohacení. Pro každou organizaci identifikovaný rozpoznávání pojmenované entity tato odborností volá webové rozhraní API najít popis dané organizace. Orchestraci při volání webového rozhraní API a jak toku získané informace interně zpracovává obohacení modul. Inicializace potřebné pro volající toto rozhraní API vlastní však je třeba zadat ve formátu JSON (například identifikátor uri, httpHeaders a očekává vstupy). Pokyny při vytváření vlastní webové rozhraní API pro obohacení kanál najdete v tématu [jak definovat vlastní rozhraní](cognitive-search-custom-skill-interface.md).

Všimněte si, že pole "context" je nastaveno ```"/document/content/organizations/*"``` s hvězdičkou, se nazývá znamená krok obohacení *pro každou* organizace v rámci ```"/document/content/organizations"```. 

Výstup, v takovém případě popis společnosti, se generuje pro každou organizaci identifikovat. Při odkazování na popis v podřízené kroku (například v klíče frázi extrakce), by použijte cestu ```"/document/content/organizations/*/description"``` Uděláte to tak. 

## <a name="enrichments-create-structure-out-of-unstructured-information"></a>Enrichments vytvořit strukturu nestrukturovaných informací o

Skillset generuje strukturovaných informací mimo Nestrukturovaná data. Podívejte se na následující příklad:

*"V jeho čtvrté čtvrtletí Microsoft zaprotokoluje $1.1 miliardy v výnosy z LinkedIn, sociálních sítí společnosti, které ho koupili poslední rok. Získání umožňuje Microsoftu kombinovat LinkedIn možnosti s jeho CRM a možnosti Office. Akcionářů jsou nadšení s průběh dosavadní."*

Pravděpodobně výsledek by byl vygenerovaný strukturu podobně jako na následujícím obrázku:

![Ukázkový výstup struktura](media/cognitive-search-defining-skillset/enriched-doc.png "ukázkový výstup struktura")

Odvolat, aby tato struktura je interní. Nelze načíst ve skutečnosti tohoto grafu v kódu.

<a name="next-step"></a>
## <a name="next-steps"></a>Další postup

Teď, když se seznámíte s obohacení kanálu a skillsets, pokračujte [jak odkazovat poznámky v skillset](cognitive-search-concept-annotations-syntax.md) nebo [mapování výstupy do polí v indexu](cognitive-search-output-field-mapping.md). 