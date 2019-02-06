---
title: Vytvoření dovedností v kanálu kognitivního vyhledávání – Azure Search
description: Definujte extrakce dat, zpracování přirozeného jazyka nebo image analýzy kroky k obohacení a extrahování strukturovaných informací z vašich dat pro použití ve službě Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 9369e076517e295a7d17011e024353614ec8ad46
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55751969"
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>Vytvoření dovedností v kanálu služby rozšíření

Kognitivní vyhledávání extrahuje a vylepšuje data tak, aby s možností vyhledávání ve službě Azure Search. Označujeme je jako kroky extrakce a rozšiřování *kognitivní dovednosti*kombinované do *dovednosti* odkazovat během indexování. Můžete použít dovedností [předdefinované dovednosti](cognitive-search-predefined-skills.md) nebo vlastních dovedností (naleznete v tématu [příklad: vytvoření vlastních dovedností](cognitive-search-create-custom-skill-example.md) Další informace).

V tomto článku se dozvíte, jak vytvořit kanál rozšíření pro dovednosti, které chcete použít. Do služby Azure Search je připojen dovedností [indexer](search-indexer-overview.md). Jednou ze součástí sady kanálu návrhu, popsaná v tomto článku je vytváření dovednosti, samotného. 

> [!NOTE]
> Další součástí návrhu kanálu je určení indexer, najdete v [dalším krokem](#next-step). Definice indexeru obsahuje odkaz na dovednosti a navíc mapování polí použité pro připojování vstupy do výstupů v cílový index.

Klíčové body:

+ Můžete mít jenom jeden dovednosti za indexeru.
+ Dovedností musí mít aspoň jeden dovedností.
+ Můžete vytvořit více dovednosti stejného typu (například varianty analýzy dovednosti obrázku).

## <a name="begin-with-the-end-in-mind"></a>Začněte s end v paměti

Doporučené prvního kroku je rozhodování o tom, jaká data extrahovat z nezpracovaných dat a způsobu použití těchto dat v řešení pro hledání. Vytváření ilustraci kanálu celé rozšíření vám může pomoct identifikovat nezbytných kroků.

Předpokládejme, že máte zájem o zpracování sadu finanční analytik komentáře. Pro každý soubor budete chtít extrahovat názvy společnosti a obecné mínění komentáře. Můžete také napsat vlastní enricher, která používá službu Bingu pro vyhledávání entit najdete další informace o společnosti, jako je například jaké obchodních společnost provozuje v. V podstatě, které mají být extrahovány informace, například následující příkaz, indexovat pro každý dokument:

| record-text | Společnosti | mínění | popisy společnosti |
|--------|-----|-----|-----|
|sample-record| ["Microsoft", "LinkedIn"] | 0,99. | ["Microsoft Corporation je American nadnárodní technologická společnost...", "LinkedIn obchodní a pracovní orientovaných na sociálních sítí..."]

Následující diagram znázorňuje kanál hypotetické rozšíření:

![Hypotetický rozšíření kanálu](media/cognitive-search-defining-skillset/sample-skillset.png "hypotetické rozšíření kanálu")


Jakmile budete mít veletrh představu, co chcete v kanálu, můžete vyjádřit dovednosti, který poskytuje tyto kroky. Funkčně zkušenostech vyjádřena při nahrávání vaší definice indexeru do služby Azure Search. Další informace o tom, jak nahrát indexer, najdete v článku [indexer dokumentaci](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


V diagramu *dokumentu hádání* krok probíhá automaticky. V podstatě Azure Search umí otevřít soubory dobře známé a vytvoří *obsah* pole obsahující text extrahovaný z každého dokumentu. Bílé polí jsou předdefinované enrichers a tečkovaná pole "Bingu pro vyhledávání entit" představuje vlastní enricher, kterou vytváříte. Jak je znázorněno, zkušenostech obsahuje tři dovednosti.

## <a name="skillset-definition-in-rest"></a>Definice dovedností v REST

Dovedností je definován jako pole dovednosti. Každou dovednosti definuje zdroj jeho vstupů a název výstupy vytvořen. Použití [vytvořit dovednosti REST API](https://docs.microsoft.com/rest/api/searchservice/create-skillset), můžete definovat dovedností, které odpovídá předchozímu diagramu: 

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

Při vytváření dovedností, můžete zadat popis aby dokumentace svým dovednosti. Popis je volitelný, ale užitečná pro udržování přehledu o co dělá dovedností. Protože znalostí je dokument JSON, která nepovoluje komentáře, je nutné použít `description` pro tento element.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Další část v zkušenostech je pole dovedností. Každou dovednosti můžete představit jako primitivem rozšíření. Každou dovednosti provede malé úlohu u tohoto rozšíření kanálu. Každé z nich přebírá vstup (nebo sadu vstupů) a vrací některé výstupy. Následujících částech se soustředit na určení předdefinované a vlastní dovednosti, zřetězení dovednosti prostřednictvím odkazů na vstupní a výstupní. Vstupy můžou pocházet ze zdroje dat nebo z jiné dovednosti. Výstupy můžete namapováno na pole v indexu vyhledávání nebo použít jako vstup pro příjem dat dovedností.

## <a name="add-predefined-skills"></a>Přidat předdefinované dovednosti

Podívejme se na první dovedností, které je předdefinovaného [dovednosti rozpoznávání entit](cognitive-search-skill-entity-recognition.md):

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

* Má každé předdefinované dovednosti `odata.type`, `input`, a `output` vlastnosti. Vlastnosti specifické pro dovedností poskytují další informace lze použít na dovednosti. Pro rozpoznávání entit `categories` je jedné entitě mezi fixní sadu typů entit, které dokáže rozpoznat pretrained modelu.

* Musí mít každý dovedností ```"context"```. Kontext představuje úroveň, kdy operace můžou probíhat. Ve výše uvedené dovedností kontext je celý dokument, což znamená, že dovedností rozpoznávání pojmenovaných entit se volá jednou pro každý dokument. Výstupy jsou také vytvářeny na této úrovni. Přesněji řečeno ```"organizations"``` jsou generovány jako člen ```"/document"```. V podřízené dovednosti, mohou odkazovat na tuto nově vytvořenou informace jako ```"/document/organizations"```.  Pokud ```"context"``` není explicitně nastavena pole, je výchozí kontext dokumentu.

* Dovednosti má jeden vstupní nazývá "text", se sadou vstupní zdroj ```"/document/content"```. Dovednosti (s názvem rozpoznávání entit) pracuje *obsah* pole každého dokumentu, což je standardní pole vytvořené indexování objektů blob v Azure. 

* Dovednosti má jeden výstup nazvaný ```"organizations"```. Výstupy existují pouze během zpracování. Zřetězit tento výstup vstupem podřízené dovednosti, odkazují na výstupu jako ```"/document/organizations"```.

* U konkrétního dokumentu, hodnota ```"/document/organizations"``` je pole organizací, které extrahují z textu. Příklad:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Některé situace potřebují pro odkazování na každý prvek pole zvlášť. Předpokládejme například, které chcete předat každý prvek ```"/document/organizations"``` samostatně na jiném dovedností (například vlastní Bingu entity hledání enricher). Můžete odkazovat na každý prvek pole přidáním hvězdičky do cesty: ```"/document/organizations/*"``` 

Druhý dovednosti pro extrakci mínění používá stejný vzor jako první enricher. Trvá ```"/document/content"``` jako vstup a vrátí skóre mínění pro každou instanci obsahu. Protože jste nenastavili ```"context"``` pole explicitně, výstup (mySentiment) je nyní podřízený ```"/document"```.

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

## <a name="add-a-custom-skill"></a>Přidání vlastních dovedností

Odvolat struktura vlastní enricher vyhledávání entit Bingu:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      }
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

Tato definice [vlastních dovedností](cognitive-search-custom-skill-web-api.md) , která volá webové rozhraní API jako součást procesu rozšíření. Pro každou organizaci identifikovaný rozpoznávání pojmenovaných entit volá tento dovedností webového rozhraní API k vyhledání popisu dané organizace. Orchestrace nad tím, kdy k volání webového rozhraní API a o tom, které jsou předávány informace získané interně zpracována třídou modul rozšíření. Ve formátu JSON (například identifikátor uri, záhlaví HTTP a očekává vstupy) ale musí být zadaná potřeby pro vlastní rozhraní API nezavolá inicializace. Pokyny k vytvoření vlastního webového rozhraní API pro rozšíření kanálu, naleznete v tématu [jak definovat vlastní rozhraní](cognitive-search-custom-skill-interface.md).

Všimněte si, že pole "kontext" nastavena na ```"/document/organizations/*"``` hvězdičkou, což znamená kroku rozšíření se nazývá *pro každou* organizace v rámci ```"/document/organizations"```. 

Výstup, v tomto případě popis společnosti, se vygeneruje pro každou organizaci identifikovat. Při odkazování na popis v kroku podřízený (například v extrakce klíčových frází), můžete využít cestu ```"/document/organizations/*/description"``` Uděláte to tak. 

## <a name="enrichments-create-structure-out-of-unstructured-information"></a>Obohacení vytvoření struktury z nestrukturovaných informace

Zkušenostech generuje strukturovaných informací z Nestrukturovaná data. Vezměte v úvahu v následujícím příkladu:

*"V jeho čtvrté čtvrtletí Microsoft přihlásili 1.1 miliard dolarů příjmy z Linkedinu, sociální sítě společnosti, kterou zakoupíte minulý rok. Získání umožňuje společnosti Microsoft kombinovat funkce Linkedinu s jeho CRM a možnosti Office. Akcionářů nadšeni, že se průběhu zatím."*

Pravděpodobně výsledkem by byl vygenerovaný struktury podobně jako na následujícím obrázku:

![Ukázkový výstup struktura](media/cognitive-search-defining-skillset/enriched-doc.png "ukázkový výstup struktura")

Připomínáme, že tato struktura je interní. Nelze načíst skutečně tohoto grafu v kódu.

<a name="next-step"></a>
## <a name="next-steps"></a>Další postup

Teď, když jste se seznámili s rozšíření kanálu a dovednosti, pokračujte [způsob vytvoření odkazu poznámky v dovedností](cognitive-search-concept-annotations-syntax.md) nebo [jak namapovat na pole v indexu výstupy](cognitive-search-output-field-mapping.md). 
