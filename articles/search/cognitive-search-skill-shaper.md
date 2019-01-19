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
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: a1f5a698ee76ebd0561bd19ff1a23d0f04be0771
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54410111"
---
#   <a name="shaper-cognitive-skill"></a>Shaper kognitivních dovedností

**Shaper** dovednosti umožňuje vytvořit komplexní typ pro podporu složeného pole (označované také jako pole s více částmi.). Komplexní typ pole má více částí, ale je považován za jednu položku v indexu Azure Search. Konsolidované pole, které jsou užitečné v situacích, hledání příklady zkombinují název první a poslední do jednoho pole, Město a stát do jednoho pole, nebo název a datum narození do jednoho pole ke zjištění jedinečné identity.

**Shaper** dovednosti umožňuje v podstatě vytvořit strukturu, definování názvu členem struktury a přiřadit hodnoty k jednotlivým členům.

Ve výchozím nastavení tato technika podporuje objekty, které jsou jednu úroveň. Pro složitější objektů můžete zřetězit několik **Shaper** kroky.

V odpovědi výstupní název je vždy "výstupní". Kanál interně, můžete namapovat jiný název, jako je například "analyzedText" v příkladech níže "výstupní", ale **Shaper** dovednosti, samotný vrátí "výstupní" v odpovědi. To může být důležité Pokud ladíte bohatších možností dokumenty a Všimněte si názvů nesrovnalosti, nebo pokud při vývoji vlastních dovedností a jsou strukturování odpovědi, sami.

> [!NOTE]
> Tato dovednosti není vázán na API služeb Cognitive Services a se vám neúčtují poplatky k jeho používání. Měli stále [připojit prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md), ale k přepsání **Free** resource – možnost, která omezuje vám malý počet denních obohacení za den.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>Příklad 1: komplexní typy

Představte si třeba situaci, ve kterém chcete vytvořit strukturu s názvem *analyzedText* , který má dva členy: *text* a *mínění*v uvedeném pořadí. Ve službě Azure Search je vícedílný prohledávatelná pole s názvem *komplexní typ*, a je ještě není podporovaný úprav. V této verzi preview **Shaper** dovednosti je možné generovat pole komplexní typ v indexu. 

Následující příklad obsahuje člen názvy jako vstup. Výstupní struktury (vaše komplexní pole ve službě Azure Search) se specifikuje prostřednictvím *targetName*. 


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
      "targetName": analyzedText"
    }
  ]
}
```

### <a name="sample-input"></a>Ukázkový vstup
Poskytnutí vstupu použitelný pro tento dokument JSON **Shaper** dovednosti může být:

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


### <a name="sample-output"></a>Ukázkový výstup
**Shaper** dovednosti vygeneruje nový prvek s názvem *analyzedText* kombinované elementy *text* a *mínění*. 

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

## <a name="sample-2-input-consolidation"></a>Příklad 2: vstupní konsolidace

V jiném příkladu Představte si, že v různých fázích zpracování kanálu, můžete extrahovat název knihy a názvy kapitol na různých stránkách knihy. Nyní můžete vytvořit jednu strukturu skládá z těchto různých vstupy.

Definici Shaper dovedností pro tento scénář může vypadat jako v následujícím příkladu:

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
            "source": "/document/content/pages/*/chapterTitles/*"
        }
    ],
    "outputs": [
        {
            "output": "titlesAndChapters",
            "targetName": "analyzedText"
        }
    ]
}
```

### <a name="sample-output"></a>Ukázkový výstup
V takovém případě Shaper sloučí všechny názvy kapitol vytvořte jedno pole. 

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

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)

