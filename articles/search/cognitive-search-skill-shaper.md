---
title: Kognitivní dovednost tvarovače
titleSuffix: Azure Cognitive Search
description: Extrahuje metadata a strukturované informace z nestrukturovaných dat a natvaruje je jako komplexní typ v kanálu rozšíření AI v Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 81eb0e60befc544a6c3bee8f04e901b6a5e472bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85560821"
---
# <a name="shaper-cognitive-skill"></a>Kognitivní dovednost tvarovače

**Shaper** dovednost konsoliduje několik vstupů do [komplexního typu](search-howto-complex-data-types.md) , na který lze později odkazovat v kanálu rozšíření. **Shaper** dovednosti umožňuje v podstatě vytvořit strukturu, definovat název členů této struktury a přiřadit hodnoty každému členu. Příklady konsolidovaných polí užitečných ve scénářích vyhledávání zahrnují kombinování jména a příjmení do jedné struktury, města a stavu do jedné struktury, nebo název a DatumNarození do jediné struktury k navázání jedinečné identity.

**Shaper** dovednosti znázorněné ve [scénáři 3](#nested-complex-types) navíc do vstupu přidávají volitelnou vlastnost *sourceContext* . Vlastnosti *source* a *sourceContext* se vzájemně vylučují. Pokud je vstup v kontextu dovednosti, stačí použít *zdroj*. Pokud je vstup v *jiném* kontextu než dovednostní kontext, použijte *sourceContext*. *SourceContext* vyžaduje, abyste definovali vnořený vstup s konkrétním prvkem, který se řeší jako zdroj. 

Název výstupu je vždy "Output". Kanál interně může namapovat jiný název, jako je například "analyzedText", jak je znázorněno v níže uvedených příkladech, ale dovednost **Shaper** sám vrátí "Output" v odpovědi. To může být důležité, pokud ladíte obohacené dokumenty a všimnete si nesouladu názvů nebo pokud vytvoříte vlastní dovednost a rozřadíte odpověď sami.

> [!NOTE]
> **Shaper** dovednost není vázána na rozhraní API Cognitive Services a za jejich použití se vám neúčtují žádné poplatky. K dispozici byste ale měli i [prostředek Cognitive Services](cognitive-search-attach-cognitive-services.md), abyste mohli přepsat možnost **bezplatného** prostředku, která omezuje na malý počet denních rozšíření za den.

## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. util. ShaperSkill

## <a name="scenario-1-complex-types"></a>Scénář 1: komplexní typy

Vezměte v úvahu scénář, ve kterém chcete vytvořit strukturu s názvem *analyzedText* , která má dva členy: *text* a *mínění*, v uvedeném pořadí. V indexu se u více částí vyhledávacího pole říká *složitý typ* a často se vytváří, když má zdrojová data odpovídající komplexní strukturu, která se na ni mapuje.

Dalším přístupem k vytváření komplexních typů je však **Shaper** dovednost. Zahrnutím této dovednosti do dovednosti mohou operace v paměti během zpracování dovednosti výstupem datových tvarů s vnořenými strukturami, které lze následně namapovat na komplexní typ v indexu. 

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

### <a name="sample-index"></a>Vzorový index

Dovednosti je vyvolán indexerem a indexer vyžaduje index. Složitá reprezentace pole v indexu může vypadat podobně jako v následujícím příkladu. 

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

### <a name="skill-input"></a>Vstup dovedností

Příchozí dokument JSON, který poskytuje použitelný vstup pro tuto **Shaper** dovednost může být:

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


### <a name="skill-output"></a>Výstup dovedností

**Shaper** dovednost generuje nový element s názvem *analyzedText* s kombinovanými prvky *textu* a *mínění*. Tento výstup odpovídá schématu indexu. Bude importována a indexována v indexu služby Azure Kognitivní hledání.

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

## <a name="scenario-2-input-consolidation"></a>Scénář 2: konsolidace vstupu

V jiném příkladu Představte si, že v různých fázích zpracování kanálu jste extrahovali název knihy a nadpisy kapitol na různých stránkách knihy. Nyní můžete vytvořit jednu strukturu složenou z těchto různých vstupů.

Definice dovedností **Shaper** pro tento scénář může vypadat jako v následujícím příkladu:

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

### <a name="skill-output"></a>Výstup dovedností
V tomto případě **Shaper** sloučí všechny nadpisy kapitol a vytvoří jedno pole. 

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

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scénář 3: konsolidace vstupu z vnořených kontextů

Představte si, že máte nadpis, kapitoly a obsah knihy a máte v obsahu spuštěné rozpoznávání entit a klíčové fráze a teď je potřeba agregovat výsledky z různých dovedností do jednoho obrazce s názvem kapitoly, entitami a klíčovými frázemi.

Definice dovedností **Shaper** pro tento scénář může vypadat jako v následujícím příkladu:

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

### <a name="skill-output"></a>Výstup dovedností
V tomto případě **Shaper** vytvoří komplexní typ. Tato struktura existuje v paměti. Pokud ho chcete uložit do [znalostní báze Knowledge Store](knowledge-store-concept-intro.md), měli byste v dovednosti vytvořit projekci, která definuje charakteristiky úložiště.

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
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Jak používat komplexní typy](search-howto-complex-data-types.md)
+ [Knowledge Store](knowledge-store-concept-intro.md)
+ [Vytvoření úložiště znalostí v REST](knowledge-store-create-rest.md)