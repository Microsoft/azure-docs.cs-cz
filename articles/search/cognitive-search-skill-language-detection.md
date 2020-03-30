---
title: Kognitivní dovednosti detekce jazyka
titleSuffix: Azure Cognitive Search
description: Vyhodnotí nestrukturovaný text a pro každý záznam vrátí identifikátor jazyka se skóre označujícím sílu analýzy v kanálu obohacení ai v Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8439788c63ec1b9feaea148ab52aba498791dc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045017"
---
#   <a name="language-detection-cognitive-skill"></a>Kognitivní dovednosti detekce jazyka

Dovednost **Rozpoznávání jazyků** detekuje jazyk vstupního textu a hlásí jeden kód jazyka pro každý dokument odeslaný na žádost. Kód jazyka je spárován se skóre označujícím sílu analýzy. Tato dovednost používá modely strojového učení poskytované [textovou analýzou](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) ve službách Cognitive Services.

Tato funkce je užitečná zejména v případě, že potřebujete poskytnout jazyk textu jako vstup do jiných dovedností (například [dovednost Analýza mínění](cognitive-search-skill-sentiment.md) nebo Dovednost rozdělení [textu](cognitive-search-skill-textsplit.md)).

Zjišťování jazyků využívá knihovny bingu pro zpracování přirozeného jazyka, které přesahují počet [podporovaných jazyků a oblastí](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) uvedených pro analýzu textu. Přesný seznam jazyků není publikován, ale obsahuje všechny široce mluvené jazyky, plus varianty, dialekty a některé regionální a kulturní jazyky. Pokud máte obsah vyjádřený v méně často používaném jazyce, můžete [zkusit rozhraní API pro detekci jazyka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) a zjistit, zda vrátí kód. Odpověď pro jazyky, které `unknown`nelze zjistit, je .

> [!NOTE]
> Při rozšiřování oboru zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI budete muset [připojit fakturovatelný prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md). Poplatky narůstají při volání API ve službách Cognitive Services a pro extrakci image jako součást fáze prolomení dokumentů v Azure Cognitive Search. Za extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Provádění vestavěných dovedností se účtuje za stávající [cenu průběžných plateb služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsané na [stránce s cenami Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu by měla být 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)znaků měřená písmenem . Pokud potřebujete rozdělit data před odesláním do dovednosti rozpoznávání jazyka, můžete použít [dovednost Rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Vstupy dovedností

U parametrů se rozlišují malá a velká písmena.

| Vstupy     | Popis |
|--------------------|-------------|
| text | Text, který má být analyzován.|

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu    | Popis |
|--------------------|-------------|
| languageCode | Kód jazyka ISO 6391 pro identifikovaný jazyk. Například "en". |
| languageName | Název jazyka. Například "Angličtina". |
| skóre | Hodnota mezi 0 a 1. Pravděpodobnost, že jazyk je správně identifikován. Skóre může být nižší než 1, pokud věta má smíšené jazyky.  |

##  <a name="sample-definition"></a>Definice vzorku

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
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
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>Chybové případy
Pokud je text vyjádřen v nepodporovaném jazyce, je generována chyba a není vrácen žádný identifikátor jazyka.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
