---
title: Kognitivní vyhledávání dovedností Shaper – Azure Search
description: Extrahování metadat a strukturovaných informací z nestrukturovaných dat a převeďte ji jako komplexní typ v rozšíření kanálu služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 5267f81c9886e2d1d8d62c134156aedb3b2b8763
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023725"
---
#   <a name="shaper-cognitive-skill"></a>Shaper kognitivních dovedností

**Shaper** dovednosti konsoliduje několik vstupy do [komplexní typ](search-howto-complex-data-types.md) , který může být odkazováno později v rozšíření kanálu. **Shaper** dovednosti umožňuje v podstatě vytvořit strukturu, definování názvu členem struktury a přiřadit hodnoty k jednotlivým členům. Konsolidované pole, které jsou užitečné v situacích, hledání příklady zkombinují název první a poslední do struktura single, Město a stát do jednoho struktury, nebo název a datum narození do jednoho struktury zřízení jedinečné identity.

Verze rozhraní API zjistí, že je hloubka tvarování můžete dosáhnout. 

| Verze API | Strukturování chování | 
|-------------|-------------------|
| 2019-05-06-preview verzi rozhraní API REST (sadu .NET SDK se nepodporuje.) | Komplexní objekty, několik úrovní hluboko, v jednom **Shaper** definice dovedností. |
| 2019-05-06 ** (všeobecně dostupné) 2017-11-11-Preview| Složité objekty jednu úroveň. Obrazec na více vyžaduje několik kroků shaper zřetězení.|

Náhled **Shaper** dovedností v [scénář 3](#nested-complex-types), přidá nový volitelný *sourceContext* vlastnost na vstup. *Zdroj* a *sourceContext* vlastnosti se vzájemně vylučují. Pokud je vstup v kontextu dovednosti, jednoduše použijte *zdroj*. Pokud je vstup na *různých* kontextu než kontextu dovednosti, použijte *sourceContext*. *SourceContext* vyžaduje, abyste definovat vnořené vstup s konkrétní elementu zabýváme jako zdroj. 

V odpovědi pro všechny verze rozhraní API výstupní název je vždy "výstupní". Kanál interně, můžete namapovat jiný název, jako je například "analyzedText", jak je znázorněno níže, ale **Shaper** dovednosti, samotný vrátí "výstupní" v odpovědi. To může být důležité Pokud ladíte bohatších možností dokumenty a Všimněte si názvů nesrovnalosti, nebo pokud při vývoji vlastních dovedností a jsou strukturování odpovědi, sami.

> [!NOTE]
> **Shaper** dovednosti není svázána API služeb Cognitive Services a neúčtují k jeho používání. Měli stále [připojit prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md), ale k přepsání **Free** resource – možnost, která omezuje vám malý počet denních obohacení za den.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Scénář 1: komplexní typy

Představte si třeba situaci, ve kterém chcete vytvořit strukturu s názvem *analyzedText* , který má dva členy: *text* a *mínění*v uvedeném pořadí. V indexu Azure Search je vícedílný prohledávatelná pole s názvem *komplexní typ* a je často vytvořena, když zdrojová data mají odpovídající složité struktury, která se mapuje na ni.

Další možností pro vytváření komplexních typů je však prostřednictvím **Shaper** dovedností. Zahrnutím této dovedností v dovedností můžete operací v paměti během zpracování dovednosti výstupní datové obrazce s vnořené struktury, které lze mapovat na komplexní typ v indexu. 

Následující příklad dovednosti definice obsahuje člen názvy jako vstup. 


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

### <a name="sample-index"></a>Ukázkového indexu

Indexer vyvolá dovedností a indexer vyžaduje indexu. Reprezentace komplexní pole v indexu může vypadat jako v následujícím příkladu. 

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

Dokument JSON příchozí poskytuje použitelné vstup pro tuto **Shaper** dovednosti může být:

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

**Shaper** dovednosti vygeneruje nový prvek s názvem *analyzedText* kombinované elementy *text* a *mínění*. Tento výstup vyhovuje schématu indexu. Budou importovány a indexování v indexu Azure Search.

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

## <a name="scenario-2-input-consolidation"></a>Scénář 2: vstupní konsolidace

V jiném příkladu Představte si, že v různých fázích zpracování kanálu, můžete extrahovat název knihy a názvy kapitol na různých stránkách knihy. Nyní můžete vytvořit jednu strukturu skládá z těchto různých vstupy.

**Shaper** definice dovedností pro tento scénář může vypadat jako v následujícím příkladu:

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
V takovém případě **Shaper** sloučí všechny názvy kapitol vytvořte jedno pole. 

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

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scénář 3: vstupní konsolidace z vnořené kontextů

> [!NOTE]
> Vnořené struktury podporované ve verzi api-version = 2019-05-06-Preview je možné v [znalostní báze úložiště](knowledge-store-concept-intro.md) nebo v indexu Azure Search.

Představte si mít název, kapitol a obsah knihy a spustili entity rozpoznávání a klíč frází na obsah a teď potřeba agregovat výsledky z jiné dovednosti do jediného obrazce s názvem kapitoly, entit a klíčové fráze.

**Shaper** definice dovedností pro tento scénář může vypadat jako v následujícím příkladu:

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
V takovém případě **Shaper** vytvoří komplexního typu. Tato struktura existuje v paměti. Pokud chcete uložit do úložiště znalostní báze, měli byste vytvořit projekce v vaše dovednosti, která definuje vlastnosti úložiště.

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

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Jak používat komplexní typy](search-howto-complex-data-types.md)
+ [Přehled úložiště znalostní báze](knowledge-store-concept-intro.md)
+ [Jak začít pracovat s Store znalostní báze](knowledge-store-howto.md)