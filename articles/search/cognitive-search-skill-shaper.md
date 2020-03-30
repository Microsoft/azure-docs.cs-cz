---
title: Kognitivní dovednost tvarovače
titleSuffix: Azure Cognitive Search
description: Extrahujte metadata a strukturované informace z nestrukturovaných dat a tvarovat je jako komplexní typ v kanálu obohacení umělou ai ve službě Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 384b79037bb30656934c5e4b596dac2b776593b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754115"
---
# <a name="shaper-cognitive-skill"></a>Kognitivní dovednost tvarovače

**Shaper** dovednost konsoliduje několik vstupů do [komplexní typ,](search-howto-complex-data-types.md) který lze odkazovat později v kanálu obohacení. **Shaper** dovednost umožňuje v podstatě vytvořit strukturu, definovat název členů této struktury a přiřadit hodnoty pro každého člena. Příklady sloučených polí užitečných ve scénářích hledání zahrnují sloučení křestního jména a příjmení do jedné struktury, města a státu do jedné struktury nebo název a datum narození do jedné struktury pro vytvoření jedinečné identity.

Navíc **Shaper** dovednost ilustrovaná ve [scénáři 3](#nested-complex-types) přidá volitelné *sourceContext* vlastnost vstup. Vlastnosti *source* a *sourceContext* se vzájemně vylučují. Pokud je vstup v kontextu dovednosti, jednoduše použijte *zdroj*. Pokud je vstup v *jiném* kontextu než kontext dovednosti, použijte *sourceContext*. *SourceContext* vyžaduje, abyste definovali vnořený vstup s konkrétním prvkem, který je adresován jako zdroj. 

Výstupní název je vždy "výstup". Interně kanálu můžete mapovat jiný název, například "analyzedText", jak je znázorněno v příkladech níže, ale **Shaper** dovednost sama vrátí "výstup" v odpovědi. To může být důležité, pokud ladíte obohacené dokumenty a všimněte si nesouladu pojmenování, nebo pokud vytvoříte vlastní dovednost a strukturujete odpověď sami.

> [!NOTE]
> **Shaper** dovednost není vázána na rozhraní API služby Cognitive Services a nejsou účtovány za jeho použití. Stále byste měli [připojit prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md), abyste však přepsali možnost **Volný** prostředek, který vás omezuje na malý počet denních obohacení za den.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Scénář 1: složité typy

Zvažte scénář, kde chcete vytvořit strukturu s názvem *analyzedText,* který má dva členy: *text* a *mínění*, v uvedeném pořadí. V indexu se vícedílné prohledávatelné pole nazývá *komplexní typ* a často se vytváří, když má zdrojová data odpovídající složitou strukturu, která se na něj mapuje.

Jiný přístup pro vytváření složitých typů je však prostřednictvím **shaper** dovednosti. Zahrnutím této dovednosti do sady dovedností mohou operace v paměti během zpracování sady dovedností výstup obrazců dat s vnořenými strukturami, které pak mohou být mapovány na komplexní typ v indexu. 

Následující příklad definice dovednosti poskytuje názvy členů jako vstup. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Index vzorku

Skillset je vyvolána indexeru a indexer vyžaduje index. Komplexní reprezentace pole v indexu může vypadat jako v následujícím příkladu. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Vstup dovednosti

Příchozí dokument JSON poskytující použitelný vstup pro tuto dovednost **Shaper** může být:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Výstup dovednosti

**Shaper** dovednost generuje nový prvek s názvem *analyzedText* s kombinovanými prvky *textu* a *mínění*. Tento výstup odpovídá schématu indexu. Bude importována a indexována v indexu Azure Cognitive Search.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>Scénář 2: konsolidace vstupů

V jiném příkladu si představte, že v různých fázích zpracování potrubí jste extrahovali název knihy a názvy kapitol na různých stránkách knihy. Nyní můžete vytvořit jednu strukturu složenou z těchto různých vstupů.

Definice dovedností **Shaper** pro tento scénář může vypadat jako následující příklad:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Výstup dovednosti
V tomto případě **Shaper** sloučí všechny názvy kapitol a vytvoří jedno pole. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scénář 3: konsolidace vstupů z vnořených kontextů

Představte si, že máte název, kapitoly a obsah knihy a máte spustit rozpoznávání entit a klíčové fráze na obsah a nyní je třeba agregovat výsledky z různých dovedností do jednoho tvaru s názvem kapitoly, entity a klíčové fráze.

Definice dovedností **Shaper** pro tento scénář může vypadat jako následující příklad:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Výstup dovednosti
V tomto případě **Shaper** vytvoří komplexní typ. Tato struktura existuje v paměti. Pokud jej chcete uložit do [úložiště znalostí](knowledge-store-concept-intro.md), měli byste vytvořit projekci v sadě dovedností, která definuje charakteristiky úložiště.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Jak používat složité typy](search-howto-complex-data-types.md)
+ [Knowledge Store (Preview)](knowledge-store-concept-intro.md)
+ [Vytvoření úložiště znalostí v REST](knowledge-store-create-rest.md)