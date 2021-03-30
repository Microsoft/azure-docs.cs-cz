---
title: Definice rozhraní pro vlastní dovednosti
titleSuffix: Azure Cognitive Search
description: Vlastní rozhraní pro extrakci dat pro vlastní dovednosti webového rozhraní API v kanálu rozšíření AI v Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: e78f0d1e8d6d637dfebe1ff475ab8416ba49a263
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88935411"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>Postup přidání vlastní dovednosti do kanálu rozšíření Azure Kognitivní hledání

> [!VIDEO https://www.youtube.com/embed/fHLCE-NZeb4?version=3&start=172&end=221]

[Kanál pro rozšíření](cognitive-search-concept-intro.md) ve službě Azure kognitivní hledání se dá sestavovat z [vestavěných schopností rozpoznávání](cognitive-search-predefined-skills.md) a také [vlastních dovedností](cognitive-search-custom-skill-web-api.md) , které do kanálu vytvoříte a přidáte. V tomto článku se dozvíte, jak vytvořit vlastní dovednost, která zveřejňuje rozhraní, což umožňuje zahrnutí do kanálu pro rozšíření AI. 

Vytváření vlastních dovedností vám dává možnost vkládat transformace, které jsou pro váš obsah jedinečné. Vlastní dovednosti se spouští nezávisle, přičemž se aplikuje libovolný krok obohacení, který požadujete. Můžete například definovat vlastní entity specifické pro pole, vytvořit vlastní modely klasifikace pro odlišení obchodních a finančních smluv a dokumentů nebo přidat dovednost rozpoznávání řeči, abyste dosáhli hlubšího přístupu do zvukových souborů pro relevantní obsah. Podrobný příklad najdete v tématu [Příklad: Vytvoření vlastní dovednosti pro obohacení AI](cognitive-search-create-custom-skill-example.md).

 Libovolná vlastní funkce, kterou požadujete, je jednoduché a jasné rozhraní pro připojení vlastní dovednosti ke zbytku kanálu rozšíření. Jediným požadavkem pro zařazení do [dovednosti](cognitive-search-defining-skillset.md) je schopnost přijímat vstupy a generovat výstupy způsobem, který je spotřební v dovednosti jako celek. Tento článek je zaměřený na vstupní a výstupní formáty, které kanál pro obohacení vyžaduje.

## <a name="web-api-custom-skill-interface"></a>Rozhraní Web API pro vlastní dovednosti

Vlastní koncové body dovedností WebAPI ve výchozím časový limit, pokud nevrátí odpověď v rámci 30 sekundového okna. Kanál indexování je synchronní a při indexování dojde k vypršení časového limitu, pokud se v tomto okně nepřijme odpověď.  Časový limit bude možné nakonfigurovat tak, aby byl až 230 sekund, nastavením parametru timeout:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

Ujistěte se, že identifikátor URI je zabezpečený (HTTPS).

V současné době je jediným mechanismem pro interakci s vlastní dovedností prostřednictvím rozhraní Web API. Potřebné webové rozhraní API musí splňovat požadavky popsané v této části.

### <a name="1--web-api-input-format"></a>1. vstupní formát webového rozhraní API


> [!VIDEO https://www.youtube.com/embed/fHLCE-NZeb4?version=3&start=294&end=340]


Webové rozhraní API musí přijmout pole záznamů, které se mají zpracovat. Každý záznam musí obsahovat "kontejner objektů a dat", který je vstupem poskytovaným pro vaše webové rozhraní API. 

Předpokládejme, že chcete vytvořit jednoduchý obohacení, který určuje první datum uvedené v textu smlouvy. V tomto příkladu dovednost přijímá jednu vstupní *contractText* jako text kontraktu. Dovednost má také jeden výstup, což je datum smlouvy. Aby bylo možné zdokonalit obohacení, vraťte tuto *contractDate* ve tvaru komplexního typu s více částmi.

Vaše webové rozhraní API by mělo být připravené přijímat dávky vstupních záznamů. Každý člen pole *hodnot* představuje vstup pro konkrétní záznam. Každý záznam je vyžadován pro následující prvky:

+ *RecordId* člen, který je jedinečný identifikátor konkrétního záznamu. Když váš obohacení vrátí výsledky, musí poskytnout tuto *recordId* , aby mohl volajícímu odpovídat výsledkům záznamu na jejich vstupu.

+ *Datový* člen, který je v podstatě kontejnerem vstupních polí pro každý záznam.

Chcete-li být konkrétnější, vaše webové rozhraní API by mělo očekávat požadavky, které vypadají takto:

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
Ve skutečnosti může být služba volána se stovkami nebo tisíci záznamů místo pouze ze tří zobrazených.

### <a name="2-web-api-output-format"></a>2. výstupní formát webového rozhraní API

Formát výstupu je sada záznamů obsahující *recordId* a kontejner objektů a dat. 

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

Tento konkrétní příklad má pouze jeden výstup, ale můžete vymezit výstup více než jedné vlastnosti. 

### <a name="errors-and-warning"></a>Chyby a varování

Jak je znázorněno v předchozím příkladu, můžete vracet chybové a varovné zprávy pro každý záznam.

## <a name="consuming-custom-skills-from-skillset"></a>Využívání vlastních dovedností z dovednosti

Když vytvoříte obohacení webového rozhraní API, můžete v rámci žádosti popsat hlavičky a parametry protokolu HTTP. Následující fragment kódu ukazuje, jak mohou být parametry požadavků a *volitelné* hlavičky protokolu HTTP popsány v rámci definice dovednosti. Hlavičky HTTP nejsou požadavkem, ale umožňují přidat do své dovednosti další možnosti konfigurace a nastavit je z definice dovednosti.

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

Tento článek se zabývá požadavky rozhraní, které jsou nezbytné pro integraci vlastní dovednosti do dovednosti. Pokud chcete získat další informace o vlastních dovednostech a dovednosti složení, klikněte na následující odkazy.

+ [Podívejte se na video o vlastních dovednostech](https://youtu.be/fHLCE-NZeb4)
+ [Dovednosti v Power: úložiště vlastních dovedností](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Příklad: Vytvoření vlastní dovednosti pro obohacení AI](cognitive-search-create-custom-skill-example.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Vytvořit dovednosti (REST)](/rest/api/searchservice/create-skillset)
+ [Jak mapovat obohacená pole](cognitive-search-output-field-mapping.md)