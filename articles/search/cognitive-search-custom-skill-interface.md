---
title: Definice rozhraní pro vlastní dovednosti
titleSuffix: Azure Cognitive Search
description: Vlastní rozhraní extrakce dat pro vlastní dovednosti webového rozhraní API v kanálu obohacení AI v Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 78f5f6eda28bed164668445b5671dad92f8dedd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500267"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>Jak přidat vlastní dovednosti do kanálu obohacení Azure Cognitive Search

[Kanál obohacení](cognitive-search-concept-intro.md) v Azure Cognitive Search lze sestavit z [předdefinovaných kognitivních dovedností](cognitive-search-predefined-skills.md) a [také vlastních dovedností,](cognitive-search-custom-skill-web-api.md) které osobně vytvoříte a přidáte do kanálu. V tomto článku se dozvíte, jak vytvořit vlastní dovednost, která zpřístupňuje rozhraní umožňující jeho zahrnutí do kanálu obohacení AI. 

Vytváření vlastních dovedností vám dává způsob, jak vložit transformace jedinečné pro váš obsah. Vlastní dovednost se provádí nezávisle, použití jakéhokoli kroku obohacení, který požadujete. Můžete například definovat vlastní entity specifické pro pole, vytvořit vlastní modely klasifikace pro rozlišení obchodních a finančních smluv a dokumentů nebo přidat dovednost rozpoznávání řeči, abyste se dostali hlouběji do zvukových souborů pro relevantní obsah. Podrobný příklad najdete v tématu [Příklad: Vytvoření vlastní dovednosti pro obohacení umělou ai .](cognitive-search-create-custom-skill-example.md)

 Bez ohledu na vlastní funkce, které požadujete, existuje jednoduché a jasné rozhraní pro připojení vlastní dovednosti ke zbytku kanálu obohacení. Jediným požadavkem pro zahrnutí do [sady dovedností](cognitive-search-defining-skillset.md) je schopnost přijímat vstupy a vyzařovat výstupy způsoby, které jsou spotřební v rámci sady dovedností jako celku. Zaměření tohoto článku je na vstupní a výstupní formáty, které vyžaduje kanál obohacení.

## <a name="web-api-custom-skill-interface"></a>Rozhraní vlastních dovedností webového rozhraní API

Vlastní koncové body dovedností WebAPI ve výchozím limitu, pokud nevrátí odpověď v rámci 30 sekund okna. Kanál indexování je synchronní a indexování způsobí chybu časového plánu, pokud není v tomto okně přijata odpověď.  Je možné nakonfigurovat časový čas na 230 sekund nastavením parametru časového času:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

Zkontrolujte, zda je identifikátor URI zabezpečený (HTTPS).

V současné době pouze mechanismus pro interakci s vlastní dovednost je prostřednictvím rozhraní webového rozhraní API. Potřeby webového rozhraní API musí splňovat požadavky popsané v této části.

### <a name="1--web-api-input-format"></a>1. Vstupní formát webového rozhraní API

Webové rozhraní API musí přijmout pole záznamů, které mají být zpracovány. Každý záznam musí obsahovat "vak vlastností", který je vstupem poskytnutým webovému rozhraní API. 

Předpokládejme, že chcete vytvořit jednoduchý enricher, který identifikuje první datum uvedené v textu smlouvy. V tomto příkladu dovednost přijímá jeden vstupní *contractText* jako text smlouvy. Dovednost má také jeden výstup, což je datum smlouvy. Chcete-li enricher zajímavější, vrátí tento *contractDate* ve tvaru vícedílného komplexního typu.

Webové rozhraní API by mělo být připraveno k přijetí dávky vstupních záznamů. Každý člen pole *hodnot* představuje vstup pro určitý záznam. Každý záznam musí mít následující prvky:

+ Člen *recordId,* který je jedinečným identifikátorem pro určitý záznam. Když váš enricher vrátí výsledky, musí poskytnout toto *recordId,* aby volající mohl porovnat výsledky záznamu s jejich vstupem.

+ Datový *data* člen, což je v podstatě sáček vstupních polí pro každý záznam.

Chcete-li být konkrétnější, podle výše uvedeného příkladu, webové rozhraní API by měl očekávat požadavky, které vypadají takto:

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
Ve skutečnosti může být vaše služba volána se stovkami nebo tisíci záznamů namísto pouze tří zde uvedených.

### <a name="2-web-api-output-format"></a>2. Výstupní formát webového rozhraní API

Formát výstupu je sada záznamů obsahujících *recordId*a vak vlastností. 

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

Tento konkrétní příklad má pouze jeden výstup, ale může výstup více než jednu vlastnost. 

### <a name="errors-and-warning"></a>Chyby a upozornění

Jak je znázorněno v předchozím příkladu, můžete vrátit chybové a varovné zprávy pro každý záznam.

## <a name="consuming-custom-skills-from-skillset"></a>Náročné vlastní dovednosti z skillset

Při vytváření webového rozhraní API obohacující, můžete popsat hlavičky protokolu HTTP a parametry jako součást požadavku. Fragment níže ukazuje, jak mohou být parametry požadavku a *volitelné* hlavičky HTTP popsány jako součást definice sady dovedností. Hlavičky PROTOKOLU HTTP nejsou požadavkem, ale umožňují přidat další možnosti konfigurace do vašich dovedností a nastavit je z definice sady dovedností.

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

## <a name="next-steps"></a>Další kroky

Tento článek se vztahuje na požadavky rozhraní nezbytné pro integraci vlastní dovednosti do skillset. Kliknutím na následující odkazy se dozvíte více o vlastních dovednostech a složení sady dovedností.

+ [Power Skills: úložiště vlastních dovedností](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Příklad: Vytvoření vlastní dovednosti pro obohacení umělou aicí](cognitive-search-create-custom-skill-example.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Vytvořit sadu dovedností (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Jak mapovat obohacená pole](cognitive-search-output-field-mapping.md)
