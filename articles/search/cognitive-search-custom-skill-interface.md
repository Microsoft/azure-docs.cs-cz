---
title: Definice rozhraní pro vlastní dovednosti v hledání rozpoznávání – Azure Search
description: Vlastní rozhraní pro extrakci dat pro vlastní dovednosti webového rozhraní API v kanálu vyhledávání rozpoznávání v Azure Search.
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 2c4af40886a81cbf8f8e11318737db05f570a1f0
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692191"
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>Postup přidání vlastní dovednosti do kanálu vyhledávání rozpoznávání

[Kanál indexování vyhledávání rozpoznávání](cognitive-search-concept-intro.md) v Azure Search lze sestavovat z [předdefinovaných dovedností](cognitive-search-predefined-skills.md) a také [vlastními dovednostmi](cognitive-search-custom-skill-web-api.md) , které vytvoříte a přidáte do kanálu. V tomto článku se dozvíte, jak vytvořit vlastní dovednost, která zveřejňuje rozhraní, což umožňuje zahrnutí do kanálu vyhledávání rozpoznávání. 

Vytváření vlastních dovedností vám dává možnost vkládat transformace, které jsou pro váš obsah jedinečné. Vlastní dovednosti se spouští nezávisle, přičemž se aplikuje libovolný krok obohacení, který požadujete. Můžete například definovat vlastní entity specifické pro pole, vytvořit vlastní modely klasifikace pro odlišení obchodních a finančních smluv a dokumentů nebo přidat dovednost rozpoznávání řeči, abyste dosáhli hlubšího přístupu do zvukových souborů pro relevantní obsah. Podrobný příklad najdete v tématu [Příklad: Vytvoření vlastní dovednosti pro hledání vnímání](cognitive-search-create-custom-skill-example.md).

 Libovolná vlastní funkce, kterou požadujete, je jednoduché a jasné rozhraní pro připojení vlastní dovednosti ke zbytku kanálu rozšíření. Jediným požadavkem pro zařazení do [dovednosti](cognitive-search-defining-skillset.md) je schopnost přijímat vstupy a generovat výstupy způsobem, který je spotřební v dovednosti jako celek. Tento článek je zaměřený na vstupní a výstupní formáty, které kanál pro obohacení vyžaduje.

## <a name="web-api-custom-skill-interface"></a>Rozhraní Web API pro vlastní dovednosti

Vlastní koncové body dovedností WebAPI ve výchozím časový limit, pokud nevrátí odpověď v rámci 30 sekundového okna. Kanál indexování je synchronní a při indexování dojde k vypršení časového limitu, pokud se v tomto okně nepřijme odpověď.  Časový limit bude možné nakonfigurovat tak, aby byl až 230 sekund, nastavením parametru timeout:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

V současné době je jediným mechanismem pro interakci s vlastní dovedností prostřednictvím rozhraní Web API. Potřebné webové rozhraní API musí splňovat požadavky popsané v této části.

### <a name="1--web-api-input-format"></a>1. vstupní formát webového rozhraní API

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

Formát výstupu je sada záznamů obsahující *recordId*a kontejner objektů a dat. 

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

Když vytvoříte obohacení webového rozhraní API, můžete v rámci žádosti popsat hlavičky a parametry protokolu HTTP. Následující fragment kódu ukazuje, jak mohou být parametry požadavků a záhlaví HTTP popsány v rámci definice dovednosti.

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

+ [Dovednosti v Power: úložiště vlastních dovedností](https://aka.ms/powerskills)
+ [Příklad: Vytvoření vlastní dovednosti pro hledání vnímání](cognitive-search-create-custom-skill-example.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Vytvořit dovednosti (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Jak mapovat obohacená pole](cognitive-search-output-field-mapping.md)
