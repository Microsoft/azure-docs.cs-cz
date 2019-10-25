---
title: Mínění vnímání znalostí
titleSuffix: Azure Cognitive Search
description: Extrahuje kladné záporné skóre mínění z textu v kanálu rozšíření AI v Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3aab703b9c5ffcb5f3280060417ce32fcec2fc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791901"
---
# <a name="sentiment-cognitive-skill"></a>Mínění vnímání znalostí

Dovednost **mínění** vyhodnocuje nestrukturovaný text podél pozitivních hodnot Continuum a pro každý záznam vrací číselné skóre mezi 0 a 1. Hodnocení blížící se 1 značí pozitivní zabarvení a hodnocení blížící se 0 značí negativní zabarvení. Tato dovednost používá v Cognitive Services modely strojového učení, které poskytuje [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) .

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů ve službě Azure Kognitivní hledání. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci imagí jsou popsané na [stránce s cenami za Azure kognitivní hledání](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. text. SentimentSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu musí být 5000 znaků měřenou [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Pokud potřebujete data před odesláním do nástroje mínění Analyzer rozdělit, použijte [dovednost rozdělení textu](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parametry dovednosti

V parametrech jsou rozlišována malá a velká písmena.

| Název parametru |                      |
|----------------|----------------------|
| defaultLanguageCode | volitelné Kód jazyka, který se má použít pro dokumenty, které nespecifikují jazyk explicitně. <br/> Zobrazit [úplný seznam podporovaných jazyků](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Vstupy dovedností 

| Název vstupu | Popis |
|--------------------|-------------|
| text | Text, který má být analyzován.|
| languageCode  |  Volitelné Řetězec, který označuje jazyk záznamů. Není-li tento parametr zadán, je použita výchozí hodnota "en". <br/>[Úplný seznam podporovaných jazyků](../cognitive-services/text-analytics/text-analytics-supported-languages.md)najdete v části.|

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu | Popis |
|--------------------|-------------|
| podtržítk | Hodnota mezi 0 a 1, která představuje mínění analyzovaného textu. Hodnoty blížící se 0 mají negativní mínění, blízko až 0,5 mají neutrální mínění a hodnoty blízko 1 mají pozitivní mínění.|


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

##  <a name="sample-input"></a>Vzorový vstup

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
Pokud je toto nastavení prázdné, nevrátí se pro tyto záznamy mínění skóre.

## <a name="error-cases"></a>Chybové případy
Pokud jazyk není podporován, je vygenerována chyba a nebude vráceno žádné mínění skóre.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
