---
title: Sentiment kognitivní dovednosti
titleSuffix: Azure Cognitive Search
description: Extrahujte skóre mínění s kladným negativním výsledkem z textu v kanálu obohacení umělou aicí v Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3aab703b9c5ffcb5f3280060417ce32fcec2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791901"
---
# <a name="sentiment-cognitive-skill"></a>Sentiment kognitivní dovednosti

**Dovednost Mínění** vyhodnotí nestrukturovaný text podél kladně negativního kontinua a pro každý záznam vrátí číselné skóre mezi 0 a 1. Skóre blížící se 1 označují pozitivní sentiment a skóre téměř 0 označují negativní sentiment. Tato dovednost používá modely strojového učení poskytované [textovou analýzou](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) ve službách Cognitive Services.

> [!NOTE]
> Při rozšiřování oboru zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI budete muset [připojit fakturovatelný prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md). Poplatky narůstají při volání API ve službách Cognitive Services a pro extrakci image jako součást fáze prolomení dokumentů v Azure Cognitive Search. Za extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Provádění vestavěných dovedností se účtuje za stávající [cenu průběžných plateb služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsané na [stránce s cenami Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu by měla být 5000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)znaků měřeno . Pokud potřebujete rozdělit data před odesláním do analyzátoru mínění, použijte [dovednost Rozdělení textu](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Název parametru |                      |
|----------------|----------------------|
| defaultLanguageCode | (nepovinné) Kód jazyka, který se má použít pro dokumenty, které explicitně neurčují jazyk. <br/> Zobrazit [úplný seznam podporovaných jazyků](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Vstupy dovedností 

| Vstupní název | Popis |
|--------------------|-------------|
| text | Text, který má být analyzován.|
| languageCode  |  (Nepovinné) Řetězec označující jazyk záznamů. Pokud tento parametr není zadán, výchozí hodnota je "en". <br/>Viz [Úplný seznam podporovaných jazyků](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu | Popis |
|--------------------|-------------|
| skóre | Hodnota mezi 0 a 1, která představuje mínění analyzovaného textu. Hodnoty blízké 0 mají negativní sentiment, téměř 0,5 mají neutrální sentiment a hodnoty blízké 1 mají pozitivní sentiment.|


##  <a name="sample-definition"></a>Definice vzorku

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
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
            "name": "score",
            "targetName": "mySentiment"
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
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
                "languageCode": "en"
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Ukázkový výstup

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>Poznámky
Pokud je prázdný, není pro tyto záznamy vráceno skóre mínění.

## <a name="error-cases"></a>Chybové případy
Pokud jazyk není podporován, je generována chyba a není vráceno žádné skóre mínění.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
