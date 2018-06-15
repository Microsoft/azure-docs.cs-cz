---
title: Službě Shaper kognitivní vyhledávání odborností (Azure Search) | Microsoft Docs
description: Extrakce metadat a strukturovaných informace z nestrukturovaných dat a utvářejí jako komplexní typ v obohacení kanál služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 311f4bd67081de567763783a9d86540eda36d9f8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33791004"
---
#   <a name="shaper-cognitive-skill"></a>Službě Shaper kognitivní dovedností

**Službě Shaper** odborností vytvoří komplexní typ pro podporu složeného pole (také označované jako pole s více částmi). Komplexní typ pole má více částí, ale je považován za jednu položku v indexu Azure Search. Konsolidované pole užitečný ve scénářích, hledání příklady kombinování název první a poslední do jednoho pole, města a stavu do jednoho pole, nebo název a datum narození do jednoho pole pro vytvoření jedinečné identity.

Službě Shaper odborností umožňuje v podstatě vytvořit strukturu, zadejte název členů této struktury a přiřadit hodnoty pro každého člena.

Ve výchozím nastavení tato technika podporuje objekty, které jsou jednu úroveň. Pro složitější objekty můžete zřetězené několik kroků službě Shaper.

V odpovědi název výstupu je vždy "výstupní". Interně kanálu můžete namapovat jiný název, jako je například "analyzedText" v následujících příkladech k "výstupní", ale službě Shaper odborností, samotné vrátí hodnotu "výstupní" v odpovědi. To může být důležité, pokud jsou ladění provádět rozšířené dokumenty a Všimněte si pojmenování nesoulad mezi databází nebo sestavení vlastní dovedností a jsou strukturování odpovědi, sami.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>Příklad 1: komplexní typy

Vezměte v úvahu scénář, kde chcete vytvořit strukturu s názvem *analyzedText* , má dva členy: *text* a *postojích*, v uvedeném pořadí. Ve službě Azure Search, se nazývá vícedílné prohledávatelné pole *komplexní typ*, a není dosud podporován mimo pole. V této verzi preview službě Shaper odborností slouží ke generování komplexního typu pole v indexu. 

Následující příklad uvádí člen názvy jako vstup. Struktura výstup (vaše komplexní pole ve službě Azure Search) se specifikuje prostřednictvím *targetName*. 


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

### <a name="sample-input"></a>Ukázka vstup
Dokument JSON poskytuje použitelné vstup pro tento službě Shaper odborností může být:

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
Službě Shaper odborností generuje nový prvek s názvem *analyzedText* kombinované elementy *text* a *postojích*. 

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

Jiný příklad Představte si, že v různých fázích zpracování kanálu, jste extrahovali název knihy a názvy kapitol na různých stránkách knihy. Nyní můžete vytvořit jeden struktura skládá z těchto různých vstupy.

Definici službě Shaper dovednosti pro tento scénář může vypadat jako v následujícím příkladu:

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
V takovém případě službě Shaper vyrovná všechny názvy kapitol vytvořit do jednoho pole. 

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
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)

