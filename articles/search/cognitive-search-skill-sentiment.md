---
title: Kognitivní vyhledávání dovedností mínění (Azure Search) | Dokumentace Microsoftu
description: Extrahujte zabarvení textu v rozšíření kanálu služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 1e4028c3a810de41efe217e6dd4347fc3bc6bf16
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730880"
---
#   <a name="sentiment-cognitive-skill"></a>Kognitivní dovednosti mínění

**Mínění** dovednosti vyhodnotí nestrukturovaného textu podél continuum pozitivní negativní a pro každý záznam, vrací číselné skóre mezi 0 a 1. Hodnocení blížící se 1 značí pozitivní zabarvení a hodnocení blížící se 0 značí negativní zabarvení.

> [!NOTE]
> Cognitive Search je ve veřejné verzi Preview. Spuštění dovednosti a extrakce image a normalizace se momentálně nabízí zdarma. Později cenách za tyto funkce bude oznámena. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu by měl být 5 000 znaků pohledu `String.Length`. Pokud je potřeba rozdělit data před odesláním analýza mínění, použijte [dovedností rozdělit textové](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parametry dovedností

Parametry rozlišují malá a velká písmena.

| Název parametru |                      |
|----------------|----------------------|
| defaultLanguageCode | (volitelné) Kód jazyka, který chcete použít pro dokumenty, které nejsou explicitně zadat jazyk. <br/> Zobrazit [úplný seznam podporovaných jazyků](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Vstupy dovedností 

| Název vstupu | Popis |
|--------------------|-------------|
| text | Text, který má být analyzován.|
| languageCode  |  (Volitelné) Řetězec označující jazyk záznamy. Pokud není tento parametr zadán, výchozí hodnota je "en". <br/>Zobrazit [úplný seznam podporovaných jazyků](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupního | Popis |
|--------------------|-------------|
| skóre | Hodnota mezi 0 a 1, který představuje mínění analyzovaný text. Hodnoty blížící se 0 mají negativní zabarvení, blízko 0,5 mají neutrální mínění a hodnoty blížící se 1 mají pozitivní mínění.|


##  <a name="sample-definition"></a>Ukázková definice

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

##  <a name="sample-input"></a>Ukázkový vstup

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
Pokud je prázdný, skóre mínění nevrátí těchto záznamů.

## <a name="error-cases"></a>Případy chyb
Pokud jazyk není podporován, je vygenerována chyba a žádná skóre mínění je vrácena.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)