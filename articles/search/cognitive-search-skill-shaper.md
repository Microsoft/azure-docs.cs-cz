---
title: Dovednosti Shaper kognitivního vyhledávání (Azure Search) | Dokumentace Microsoftu
description: Extrahování metadat a strukturovaných informací z nestrukturovaných dat a převeďte ji jako komplexní typ v rozšíření kanálu služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 286e1f9d6f6ae09d98aa87b447df7a7524642a1f
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45728993"
---
#   <a name="shaper-cognitive-skill"></a>Shaper kognitivních dovedností

**Shaper** dovednosti umožňuje vytvořit komplexní typ pro podporu složeného pole (označované také jako pole s více částmi.). Komplexní typ pole má více částí, ale je považován za jednu položku v indexu Azure Search. Konsolidované pole, které jsou užitečné v situacích, hledání příklady zkombinují název první a poslední do jednoho pole, Město a stát do jednoho pole, nebo název a datum narození do jednoho pole ke zjištění jedinečné identity.

Dovednosti Shaper umožňuje v podstatě vytvořit strukturu, definování názvu členem struktury a přiřadit hodnoty k jednotlivým členům.

Ve výchozím nastavení tato technika podporuje objekty, které jsou jednu úroveň. Pro složitější objektů můžete zřetězit několik kroků Shaper.

V odpovědi výstupní název je vždy "výstupní". Interně kanálu můžete namapovat jiný název, jako je například "analyzedText" v následujících příkladech "výstupní", ale Shaper dovednosti, samotný vrátí hodnotu "výstupní" v odpovědi. To může být důležité Pokud ladíte bohatších možností dokumenty a Všimněte si názvů nesrovnalosti, nebo pokud při vývoji vlastních dovedností a jsou strukturování odpovědi, sami.

> [!NOTE]
> Cognitive Search je ve veřejné verzi Preview. Spuštění dovednosti a extrakce image a normalizace se momentálně nabízí zdarma. Později cenách za tyto funkce bude oznámena. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>Příklad 1: komplexní typy

Představte si třeba situaci, ve kterém chcete vytvořit strukturu s názvem *analyzedText* , který má dva členy: *text* a *mínění*v uvedeném pořadí. Ve službě Azure Search je vícedílný prohledávatelná pole s názvem *komplexní typ*, a je ještě není podporovaný úprav. V této verzi preview je možné Shaper dovednosti k vygenerování složitého typu pole v indexu. 

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
Dokument JSON poskytuje použitelné vstup pro tuto Shaper dovednost může být:

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
Dovednosti Shaper vygeneruje nový prvek s názvem *analyzedText* kombinované elementy *text* a *mínění*. 

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

