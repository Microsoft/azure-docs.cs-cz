---
title: Vytvoření sady dovedností
titleSuffix: Azure Cognitive Search
description: Definujte extrakci dat, zpracování přirozeného jazyka nebo kroky analýzy obrázků, abyste obohatili a extrahovali strukturované informace z vašich dat pro použití v Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 43251783cbcd6501562913b7b9cafb4f9f7cb3f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754570"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Jak vytvořit sadu dovedností v kanálu obohacení AI v Azure Cognitive Search 

Obohacení ai extrahuje a obohacuje data tak, aby bylo možné je prohledávat v Azure Cognitive Search. Nazýváme extrakce a obohacení kroky *kognitivní dovednosti*, kombinované do *skillset* odkazuje při indexování. Sada dovedností můžete použít [vestavěné dovednosti](cognitive-search-predefined-skills.md) nebo vlastní dovednosti (viz [příklad: Vytvoření vlastní dovednosti v kanálu obohacení AI](cognitive-search-create-custom-skill-example.md) pro další informace).

V tomto článku se dozvíte, jak vytvořit kanál obohacení pro dovednosti, které chcete použít. Skillset je připojen k [indexeru](search-indexer-overview.md)Azure Cognitive Search . Jedna část návrhu kanálu, na které se vztahuje tento článek, je vytváření skillset sám. 

> [!NOTE]
> Další částí návrhu potrubí je určení indexeru, který je popsán v [dalším kroku](#next-step). Definice indexeru obsahuje odkaz na skillset plus mapování polí používané pro připojení vstupů k výstupům v cílovém indexu.

Klíčové body k zapamatování:

+ Můžete mít pouze jednu skillset na indexer.
+ Skillset musí mít alespoň jednu dovednost.
+ Můžete vytvořit více dovedností stejného typu (například varianty dovednosti analýzy obrazu).

## <a name="begin-with-the-end-in-mind"></a>Začněte s koncem v mysli

Doporučeným počátečním krokem je rozhodnutí, která data chcete extrahovat z nezpracovaných dat a jak tato data chcete použít ve vyhledávacím řešení. Vytvoření ilustrace celého kanálu obohacení vám může pomoci určit potřebné kroky.

Předpokládejme, že máte zájem o zpracování sady připomínek finančního analytika. Pro každý soubor chcete extrahovat názvy společností a obecné mínění komentáře. Můžete také napsat vlastní obohacovací zařízení, které používá službu Vyhledávání entit Bingu k vyhledání dalších informací o společnosti, například jaký druh podnikání se společnost zabývá. V podstatě chcete extrahovat informace, jako je následující, indexované pro každý dokument:

| záznam-text | Společnosti | Sentiment | popisy společností |
|--------|-----|-----|-----|
|ukázkový záznam| ["Microsoft", "LinkedIn"] | 0.99 | ["Microsoft Corporation je americká nadnárodní technologická společnost ..." , "LinkedIn je sociální sítě orientované na podnikání a zaměstnanost..."]

Následující diagram znázorňuje hypotetický kanál obohacení:

![Hypotetický obohacovací kanál](media/cognitive-search-defining-skillset/sample-skillset.png "Hypotetický obohacovací kanál")


Jakmile budete mít reálnou představu o tom, co chcete v kanálu, můžete vyjádřit skillset, který poskytuje tyto kroky. Funkčně skillset je vyjádřena při nahrání definice indexeru azure kognitivní vyhledávání. Další informace o nahrání indexeru naleznete v [dokumentaci k indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


V diagramu se krok *prolomení dokumentu* stane automaticky. Azure Cognitive Search v podstatě ví, jak otevřít známé soubory a vytvoří pole *obsahu* obsahující text extrahovaný z každého dokumentu. Bílá pole jsou vestavěné obohacovače a tečkované pole "Hledání entit Bingu" představuje vlastní obohacovací zařízení, které vytváříte. Jak je znázorněno, skillset obsahuje tři dovednosti.

## <a name="skillset-definition-in-rest"></a>Definice sady dovedností v REST

Skillset je definován jako pole dovedností. Každá dovednost definuje zdroj jeho vstupů a název vytvořených výstupů. Pomocí [rozhraní Create Skillset REST API](https://docs.microsoft.com/rest/api/searchservice/create-skillset)můžete definovat sadu dovedností, která odpovídá předchozímu diagramu: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
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
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
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

## <a name="create-a-skillset"></a>Vytvoření sady dovedností

Při vytváření sady dovedností můžete poskytnout popis, aby se sada dovedností sama dokumentovala. Popis je volitelný, ale užitečný pro sledování toho, co skillset dělá. Vzhledem k tomu, že skillset je dokument JSON, který neumožňuje komentáře, musíte pro to použít `description` prvek.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Dalším kouskem v skillset je řada dovedností. Každou dovednost si můžete myslet jako primitivní obohacení. Každá dovednost provádí malý úkol v tomto kanálu obohacení. Každý z nich má vstup (nebo sadu vstupů) a vrátí některé výstupy. V několika následujících částech se zaměřují na to, jak určit vestavěné a vlastní dovednosti, řetězení dovednosti dohromady prostřednictvím vstupních a výstupních odkazů. Vstupy mohou pocházet ze zdrojových dat nebo z jiné dovednosti. Výstupy lze mapovat na pole v indexu vyhledávání nebo použít jako vstup pro navazující dovednost.

## <a name="add-built-in-skills"></a>Přidání vestavěných dovedností

Podívejme se na první dovednost, která je vestavěná [dovednost rozpoznávání entit](cognitive-search-skill-entity-recognition.md):

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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

* Každá předdefinovaná `odata.type`dovednost `input`má `output` vlastnosti , a vlastnosti. Vlastnosti specifické pro dovednosti poskytují další informace použitelné pro tuto dovednost. Pro rozpoznávání `categories` entit je jedna entita mezi pevnou sadou typů entit, které může předem vyzývaný model rozpoznat.

* Každá dovednost by ```"context"```měla mít . Kontext představuje úroveň, na které probíhají operace. Ve výše uvedené dovednosti je kontext celý dokument, což znamená, že schopnost rozpoznávání entity se nazývá jednou za dokument. Výstupy jsou také vyráběny na této úrovni. Přesněji ```"organizations"``` řečeno, jsou generovány ```"/document"```jako člen . V následných dovednostech můžete tyto nově ```"/document/organizations"```vytvořené informace odkázat jako na .  Pokud ```"context"``` pole není explicitně nastaveno, je výchozím kontextem dokument.

* Dovednost má jeden vstup s názvem "text", ```"/document/content"```se zdrojovým vstupem nastaveným na . Dovednost (rozpoznávání entit) funguje na poli *obsahu* každého dokumentu, což je standardní pole vytvořené indexerem objektů blob Azure. 

* Dovednost má jeden ```"organizations"```výstup s názvem . Výstupy existují pouze během zpracování. Chcete-li tento výstup zřetězit na vstup navazující dovednosti, odkazujte na výstup jako ```"/document/organizations"```.

* Pro určitý dokument ```"/document/organizations"``` je hodnota pole organizací extrahovaných z textu. Například:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Některé situace vyžadují odkazování na každý prvek pole samostatně. Předpokládejme například, že chcete ```"/document/organizations"``` předat každý prvek samostatně jiné dovednosti (například vlastní bing entity vyhledávání enricher). Na každý prvek pole můžete odkazovat přidáním hvězdičky do cesty:```"/document/organizations/*"``` 

Druhá dovednost pro extrakci mínění se řídí stejným vzorem jako první enricher. Trvá ```"/document/content"``` jako vstup a vrátí skóre mínění pro každou instanci obsahu. Vzhledem k tomu, že jste pole explicitně nenastavili, ```"context"``` výstup (mySentiment) je nyní podřízeným položkou ```"/document"```.

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

## <a name="add-a-custom-skill"></a>Přidání vlastní dovednosti

Navzpomenete strukturu vlastního obohacení entit Bing:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
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

Tato definice je [vlastní dovednost,](cognitive-search-custom-skill-web-api.md) která volá webové rozhraní API jako součást procesu obohacení. Pro každou organizaci určenou podle rozpoznávání entit tato dovednost volá webové rozhraní API k nalezení popisu této organizace. Orchestrace, kdy volat webové rozhraní API a jak tok přijaté informace je zpracována interně obohacování motoru. Inicializace nezbytná pro volání tohoto vlastního rozhraní API však musí být k dispozici v JSON (například uri, httpHeaders a očekávané vstupy). Pokyny při vytváření vlastního webového rozhraní API pro kanál obohacení naleznete v [tématu Jak definovat vlastní rozhraní](cognitive-search-custom-skill-interface.md).

Všimněte si, že pole ```"/document/organizations/*"``` "kontext" je nastaveno na hvězdičku, což ```"/document/organizations"```znamená, že krok obohacení je volán pro *každou* organizaci v části . 

Výstup, v tomto případě popis společnosti, je generován pro každou identifikovanou organizaci. Když odkazujete na popis v navazujícím kroku (například v extrakci klíčových frází), použijte k tomu cestu. ```"/document/organizations/*/description"``` 

## <a name="add-structure"></a>Přidat strukturu

Skillset generuje strukturované informace z nestrukturovaných dat. Uvažujte následující příklad:

*"Ve čtvrtém čtvrtletí společnost Microsoft zaznamenala příjmy společnosti LinkedIn, která se nachází na sociálních sítích, kterou koupila v loňském roce, 1,1 miliardy dolarů. Akvizice umožňuje společnosti Microsoft kombinovat funkce LinkedIn s funkcemi CRM a Office. Akcionáři jsou z dosavadního pokroku nadšeni."*

Pravděpodobným výsledkem by byla vygenerovaná struktura podobná následujícímu obrázku:

![Vzorová výstupní struktura](media/cognitive-search-defining-skillset/enriched-doc.png "Vzorová výstupní struktura")

Až do teď tato struktura byla pouze interní, pouze pro paměť a používá se pouze v indexech Azure Cognitive Search. Přidání úložiště znalostí vám dává způsob, jak ušetřit tvarované obohacení pro použití mimo vyhledávání.

## <a name="add-a-knowledge-store"></a>Přidání úložiště znalostí

[Úložiště znalostí](knowledge-store-concept-intro.md) je funkce náhledu v Azure Cognitive Search pro uložení obohaceného dokumentu. Úložiště znalostí, které vytvoříte, podporované účtem úložiště Azure, je úložiště, kde vaše obohacená data přistane. 

Definice úložiště znalostí je přidána do sady dovedností. Návod celého procesu naleznete v [tématu Vytvoření úložiště znalostí v rest](knowledge-store-create-rest.md).

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Můžete uložit obohacené dokumenty jako tabulky s hierarchickými relacemi zachovanými nebo jako dokumenty JSON v úložišti objektů blob. Výstup z libovolné dovednosti v skillset lze získat jako vstup pro projekci. Pokud chcete promítnout data do určitého tvaru, aktualizovaná [dovednost shaperu](cognitive-search-skill-shaper.md) může nyní modelovat složité typy, které můžete použít. 

<a name="next-step"></a>

## <a name="next-steps"></a>Další kroky

Nyní, když jste obeznámeni s kanálem obohacení a skillsets, pokračujte [s odkazem na poznámky v skillset](cognitive-search-concept-annotations-syntax.md) nebo [Jak mapovat výstupy na pole v indexu](cognitive-search-output-field-mapping.md). 
