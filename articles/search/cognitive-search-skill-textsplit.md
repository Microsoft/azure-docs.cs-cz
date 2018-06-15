---
title: Text rozdělení odborností kognitivní vyhledávání (Azure Search) | Microsoft Docs
description: Rozdělte text na bloky dat nebo stránek textu podle délku v obohacení kanál služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: dbb9261cfce0a8437cfe76121fa16aa87c4b3393
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33791025"
---
#   <a name="text-split-cognitive-skill"></a>Text rozdělení kognitivní dovedností

**Text rozdělení** odborností dělí text do bloků textu. Můžete zadat, zda chcete rozdělit text do věty nebo do stránky určité délky. Tato znalostí je obzvláště užitečné, pokud existují požadavky na délku v jiných dovednosti po proudu maximální text. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parametry dovedností

Parametry jsou malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| textSplitMode      | "Stránky" nebo "věty" | 
| maximumPageLength | Pokud textSplitMode nastavena na "stránky", vztahuje se na stránce maximální délku měřený podle `String.Length`. Minimální hodnota je 100. | 
| defaultLanguageCode   | (volitelné) Jeden z následujících kódů jazyk: `da, de, en, es, fi, fr, it, ko, pt`. Výchozí hodnota je angličtina (en). Několik co je třeba zvážit:<ul><li>Pokud předáte formátu languagecode countrycode, se používá jenom část languagecode formátu.</li><li>Pokud jazyk není v předchozím seznamu, rozdělení odborností dělí text na znak hranice.</li><li>Poskytnutí kód jazyka je užitečné, aby se zabránilo vyjímání slova v polovině jazyků není mezera, například čínština, japonština nebo korejština.</li></ul>  |


## <a name="skill-inputs"></a>Vstupy dovedností

| Název parametru       | Popis      |
|----------------------|------------------|
| Text  | Text, který chcete rozdělit na dílčí řetězec. |
| languageCode  | (Volitelné) Kód jazyka pro dokument.  |

## <a name="skill-outputs"></a>Výstupy dovedností 

| Název parametru     | Popis |
|--------------------|-------------|
| textItems | Pole dílčích řetězců, které se extrahují. |


##  <a name="sample-definition"></a>Ukázka definice

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
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
                "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
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
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Případech chyb
Pokud není podporován jazyk, se generuje upozornění a text je rozdělená na znak hranice.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)
