---
title: Kognitivní dovednost extrakce klíčových frází
titleSuffix: Azure Cognitive Search
description: Vyhodnotí nestrukturovaný text a pro každý záznam vrátí seznam klíčových frází v kanálu obohacení AI v Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ccdd25d82af2b4893260af18dac818816d9e4579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791978"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Kognitivní dovednost extrakce klíčových frází

Dovednost **Extrakce klíčových frází** vyhodnocuje nestrukturovaný text a pro každý záznam vrátí seznam klíčových frází. Tato dovednost používá modely strojového učení poskytované [textovou analýzou](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) ve službách Cognitive Services.

Tato funkce je užitečná, pokud potřebujete rychle identifikovat hlavní body rozhovoru v záznamu. Například daný vstupní text "Jídlo bylo vynikající a tam byl nádherný personál", služba vrací "jídlo" a "nádherný personál".

> [!NOTE]
> Při rozšiřování oboru zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI budete muset [připojit fakturovatelný prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md). Poplatky narůstají při volání API ve službách Cognitive Services a pro extrakci image jako součást fáze prolomení dokumentů v Azure Cognitive Search. Za extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Provádění vestavěných dovedností se účtuje za stávající [cenu průběžných plateb služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsané na [stránce s cenami Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu by měla být 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)znaků měřená písmenem . Pokud potřebujete rozdělit data před odesláním do klíčové fráze extraktoru, zvažte použití [dovednosti Rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Vstupy                | Popis |
|---------------------|-------------|
| defaultLanguageCode | (Nepovinné) Kód jazyka, který se má použít pro dokumenty, které explicitně neurčují jazyk.  Pokud není zadán výchozí kód jazyka, angličtina (en) bude použita jako výchozí kód jazyka. <br/> Viz [Úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Nepovinné) Maximální počet klíčových frází k vytvoření. |

## <a name="skill-inputs"></a>Vstupy dovedností

| Vstupy     | Popis |
|--------------------|-------------|
| text | Text, který má být analyzován.|
| languageCode  |  Řetězec označující jazyk záznamů. Pokud tento parametr není zadán, bude k analýze záznamů použit výchozí kód jazyka. <br/>Zobrazit [úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Definice vzorku

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-input"></a>Vstup vzorku

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>Ukázkový výstup

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Chyby a varování
Pokud zadáte nepodporovaný kód jazyka, je generována chyba a klíčové fráze nejsou extrahovány.
Pokud je text prázdný, zobrazí se upozornění.
Pokud je text větší než 50 000 znaků, bude analyzováno pouze prvních 50 000 znaků a bude vydáno upozornění.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
