---
title: Definice rozhraní pro vlastní znalosti kognitivního vyhledávání – Azure Search
description: Vlastní data extrakce rozhraní pro webové rozhraní api vlastních dovedností v kanálu kognitivního vyhledávání ve službě Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: e4fe511228f6e80a17af8325ee74ae0927a760bd
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754723"
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>Přidání vlastních dovedností do kanálu kognitivního vyhledávání

A [kanál indexování kognitivního vyhledávání](cognitive-search-concept-intro.md) ve službě Azure Search lze sestavit z [předdefinované dovednosti](cognitive-search-predefined-skills.md) stejně jako [vlastních dovedností](cognitive-search-custom-skill-web-api.md) , které sami vytvoříte a přidáte do kanál. V tomto článku najdete informace o vytváření vlastních dovedností, který zpřístupňuje rozhraní, díky kterému jej mají být zahrnuty v kanálu kognitivního vyhledávání. 

Vytváření vlastních dovedností poskytuje způsob, jak vložit transformace, které jsou jedinečné pro váš obsah. Vlastní dovednosti spustí nezávisle na sobě použití libovolné rozšíření kroku budete potřebovat. Může například definovat vlastní entity specifické pro pole, vytvářet vlastní klasifikace modely k rozlišení firmy a finanční smlouvy a dokumenty nebo přidání rozpoznávání řeči odbornost umožňují hlubší analýzu zvuku relevantní obsah souboru. Podrobný příklad naleznete v tématu [příklad: vytváření vlastních dovedností](cognitive-search-create-custom-skill-example.md).

 Jakýkoli vlastní funkce budete potřebovat, je k dispozici jednoduchý a jasný rozhraní pro připojení k obohacení zbytku vlastních dovedností. Jediným požadavkem pro zařazení [dovednosti](cognitive-search-defining-skillset.md) je možnost přijímat vstupy a výstupy způsoby, které nejsou použitelné v rámci dovednosti jako celek vysílat. Hlavním cílem tohoto článku je na vstupních a výstupních formátů, které vyžaduje rozšíření kanálu.

## <a name="web-api-custom-skill-interface"></a>Rozhraní Web API vlastních dovedností

Vlastní WebAPI dovednosti koncových bodů ve výchozí hodnota časového limitu, pokud není odpověď v rámci časového období 30 druhý vrátí. Kanál indexování je synchronní a indexování způsobí vypršení časového limitu v Pokud odpověď přijata nebude v tomto okně.  Je možné nakonfigurovat vypršení časového limitu bude až 90 sekund, tak, že nastavíte parametr časového limitu:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 90 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT90S",
```

V současné době je pouze mechanismus pro interakci se vlastní dovednosti pomocí rozhraní Web API. Webové rozhraní API vyžaduje, musí splňovat požadavky popsané v této části.

### <a name="1--web-api-input-format"></a>1.  Webové rozhraní API vstupní formát

Webové rozhraní API, musíte přijmout polem záznamů ke zpracování. Každý záznam může obsahovat "vlastnosti kontejneru objektů a dat", který je vstup pro vaše webové rozhraní API. 

Předpokládejme, že chcete vytvořit jednoduchou enricher, který identifikuje první datum uvedené v textu kontraktu. V tomto příkladu dovednosti přijímá jeden vstup *contractText* jako text smlouvy. Dovednosti má také jeden výstup, což je datum smlouvy. Chcete-li enricher zajímavější, vraťte se tím *contractDate* ve tvaru vícedílný komplexního typu.

Webové rozhraní API by měl být připravena přijímat dávky záznamů vstupní. Každý člen *hodnoty* pole představuje vstup pro konkrétní záznam. Každý záznam musí mít následující prvky:

+ A *recordId* člena, který je jedinečný identifikátor pro konkrétní záznam. Pokud vaše enricher vrátí výsledky, musí poskytovat to *recordId* aby volající tak, aby odpovídaly výsledky záznam pro svůj vstup.

+ A *data* člena, který je v podstatě kontejner vstupní pole pro každý záznam.

Jako další konkrétní na výše uvedeném příkladu webového rozhraní API byste očekávat, že požadavky, které vypadat nějak takto:

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
Ve skutečnosti vaše služba může zavolána stovky nebo tisíce záznamů namísto pouze tři je vidět tady.

### <a name="2-web-api-output-format"></a>2. Webové rozhraní API výstupní formát

Formát výstupu je sada záznamů obsahující *recordId*a kontejner objektů 

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

Tomto konkrétním příkladu má pouze jeden výstup, ale může výstup více než jednu vlastnost. 

### <a name="errors-and-warning"></a>Chyby a upozornění

Jak je znázorněno v předchozím příkladu, může vrátit chybové zprávy a upozornění pro každý záznam.

## <a name="consuming-custom-skills-from-skillset"></a>Použití vlastních dovedností ze znalostí

Při vytváření enricher webového rozhraní API můžete popsat hlavičky protokolu HTTP a parametry, jako součást požadavku. Následující fragment kódu ukazuje, jak parametry požadavku a hlavičky protokolu HTTP může být popsány jako součást definice dovednosti.

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

+ [Příklad: Vytváření vlastních dovedností pro rozhraní API pro překlad textu](cognitive-search-create-custom-skill-example.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Vytvoření dovedností (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Způsob mapování polí bohatších možností](cognitive-search-output-field-mapping.md)
