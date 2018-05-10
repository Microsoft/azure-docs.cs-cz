---
title: Postojích kognitivní vyhledávání odborností (Azure Search) | Microsoft Docs
description: Extrahuje postojích z textu v obohacení kanál služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
documentationcenter: ''
ms.assetid: ''
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 1ddbba5b881cd05a997cd24a9396d5b722376e6f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
#   <a name="sentiment-cognitive-skill"></a>Kognitivní odborností postojích

**Postojích** odborností vyhodnotí nestrukturovaných text podél poměrně kladné záporné a pro každý záznam, vrátí číselný skóre mezi 0 a 1. Hodnocení blížící se 1 značí pozitivní zabarvení a hodnocení blížící se 0 značí negativní zabarvení.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu musí být 5000 znaků měřený podle `String.Length`. Pokud potřebujete rozdělit vaše data před odesláním do postojích analyzátor, použijte [Text rozdělení odborností](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parametry dovedností

Parametry jsou malá a velká písmena.

| Název parametru |                      |
|----------------|----------------------|
| defaultLanguageCode | (volitelné) Kód jazyka, který chcete použít pro dokumenty, které nejsou explicitně zadat jazyk. <br/> V tématu [úplný seznam podporovaných jazyků](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Vstupy dovedností 

| Název vstupu | Popis |
|--------------------|-------------|
| Text | Text, který má být analyzován.|
| languageCode  |  (Volitelné) Řetězec označující jazyk záznamy. Pokud není tento parametr zadán, výchozí hodnota je "en". <br/>V tématu [úplný seznam podporovaných jazyků](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu | Popis |
|--------------------|-------------|
| Skóre | Hodnota mezi 0 a 1, který představuje postojích analyzovaných textu. Hodnoty blízko 0 mají záporný postojích, blíží 0,5 mít neutrální postojích a hodnoty blíží 1 mají kladné postojích.|


##  <a name="sample-definition"></a>Ukázka definice

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

##  <a name="sample-input"></a>Ukázka vstup

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
Pokud je prázdný, nevrátí se postojích skóre pro tyto záznamy.

## <a name="error-cases"></a>Případech chyb
Pokud jazyk není podporován, je generována chyba a žádná postojích skóre se vrátí.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)