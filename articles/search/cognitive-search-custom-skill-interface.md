---
title: Rozhraní definice pro vlastní dovednosti v kanálu kognitivní vyhledávání (Azure Search) | Microsoft Docs
description: Vlastní data extrakce rozhraní pro vlastní odborností webového rozhraní api v kanálu kognitivní vyhledávání ve službě Azure Search.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 8f21a56982189aa13745f27f0fae49310ae55aa0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640315"
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>Jak přidat vlastní dovednosti pro kanál kognitivní vyhledávání

V tomto článku zjistěte, jak přidat vlastní dovednosti pro kanál kognitivní vyhledávání. A [kanál indexování kognitivní vyhledávání](cognitive-search-concept-intro.md) ve službě Azure Search lze sestavit z [předdefinované dovednosti](cognitive-search-predefined-skills.md) a vlastní znalosti, které osobní vytvoříte a přidáte do kanálu.

Vytváření vlastních odborností poskytuje způsob, jak vložit transformace, které jsou jedinečné pro obsah. Vlastní odborností provede nezávisle, použití ať obohacení kroku budete potřebovat. Může například definovat vlastní entity specifické pro pole, vytvářet vlastní klasifikaci modely k odlišení obchodní a finanční smlouvy a dokumenty nebo přidání znalostí rozpoznávání řeči dosáhnout hlubší do zvukových souborů pro relevantní obsah. Podrobný příklad najdete v tématu [příklad: vytvoření vlastní odborností](cognitive-search-create-custom-skill-example.md).

 Ať vlastní funkce potřebujete, není jednoduchý a jasný rozhraní pro připojení vlastní odborností s ostatními obohacení kanálu. Jediný požadavek pro zařazení [skillset](cognitive-search-defining-skillset.md) je možnost přijmout vstupy a výstupy způsoby, které jsou v rámci skillset jako celek použití emitování. Vstupní a výstupní formátů, které vyžaduje obohacení kanálu je aktivní tohoto článku.

## <a name="web-api-custom-skill-interface"></a>Webové rozhraní API vlastní odborností rozhraní

V současné době pouze mechanismus pro interakci s vlastní znalostí je pomocí rozhraní Web API. Rozhraní Web API vyžaduje musí splňovat požadavky popsané v této části.

### <a name="1--web-api-input-format"></a>1.  Formát webové rozhraní API vstup

Rozhraní Web API musí přijmout pole záznamy mají být zpracovány. Každý záznam musí obsahovat "kontejneru objektů" tedy vstup pro webové rozhraní API. 

Předpokládejme, že chcete vytvořit jednoduché enricher, který identifikuje prvního dne uvedeného v text tohoto kontraktu. V tomto příkladu dovednosti přijímá jeden vstup *contractText* jako text smlouvy. Dovednosti má také jediného výstupu, který je datum smlouvy. Chcete-li enricher zajímavějšího, vrátí tato *contractDate* ve tvaru vícedílné komplexního typu.

Webové rozhraní API by měla být připraveno přijmout dávky vstupní záznamy. Každý člen *hodnoty* pole reprezentuje vstup o určitém záznamu. Každý záznam musí mít následující prvky:

+ A *recordId* člena, který je jedinečný identifikátor pro konkrétní záznam. Pokud vaše enricher vrátí výsledky, se musí zadat to *recordId* Chcete-li povolit volající tak, aby odpovídaly výsledky záznam k jejich zadání.

+ A *data* člena, který je v podstatě kontejner vstupní pole pro každý záznam.

Jako další konkrétní za v příkladu výše, webové rozhraní API by měl očekávat požadavků, které vypadají takto:

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
Ve skutečnosti může zavolána služby se stovkami nebo tisíci záznamů místo jenom tři zobrazeny zde.

### <a name="2-web-api-output-format"></a>2. Webové rozhraní API výstupní formát

Formát výstupu je sada záznamů obsahujících *recordId*a kontejner objektů 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

Tomto konkrétním příkladu má jenom jeden výstup, ale může výstup více než jednu vlastnost. 

### <a name="errors-and-warning"></a>Chyby a upozornění

Jak ukazuje předchozí příklad, může vrátit chybové zprávy a upozornění pro každý záznam.

## <a name="consuming-custom-skills-from-skillset"></a>Použití vlastní znalosti z skillset

Při vytváření enricher webového rozhraní API můžete popsat hlavičky protokolu HTTP a parametry, jako součást požadavku. Následující fragment kódu ukazuje, jak parametrů žádosti a hlavičky protokolu HTTP může být popsané jako součást definice skillset.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Další postup

+ [Příklad: Vytvoření vlastní dovednosti pro rozhraní API převede Text](cognitive-search-create-custom-skill-example.md)
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)
+ [Vytvoření Skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Postup mapování provádět rozšířené pole](cognitive-search-output-field-mapping.md)
