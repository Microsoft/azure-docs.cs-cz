---
title: Kognitivní dovednosti rozdělení textu
titleSuffix: Azure Cognitive Search
description: Rozdělte text na bloky nebo stránky textu na základě délky v kanálu obohacení ai v Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3f80169808b1e6420f04b786d2bb06bde9c96231
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73479667"
---
# <a name="text-split-cognitive-skill"></a>Kognitivní dovednosti rozdělení textu

Dovednost **Rozdělení textu** rozdělí text na bloky textu. Můžete určit, zda chcete rozdělit text na věty nebo na stránky určité délky. Tato dovednost je užitečná zejména v případě, že existují požadavky na maximální délku textu v jiných dovednostech po proudu. 

> [!NOTE]
> Tato dovednost není vázána na rozhraní API služby Cognitive Services a za jeho použití se vám neúčtují poplatky. Stále byste měli [připojit prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md), abyste však přepsali možnost **Volný** prostředek, který vás omezuje na malý počet denních obohacení za den.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| textSplitMode      | "stránky" nebo "věty" | 
| maximální délka stránky | Pokud textSplitMode je nastavena na "stránky", to odkazuje `String.Length`na maximální délku stránky měřeno . Minimální hodnota je 100.  Pokud je textSplitMode nastaven na "stránky", algoritmus se pokusí rozdělit text na bloky, které jsou maximálně "maximumPageLength" ve velikosti. V tomto případě algoritmus bude dělat to nejlepší přerušit větu na hranici věty, takže velikost bloku může být o něco menší než "maximumPageLength". | 
| defaultLanguageCode   | (nepovinné) Jeden z následujících `da, de, en, es, fi, fr, it, ko, pt`kódů jazyka: . Výchozí hodnota je angličtina (en). Jen málo věcí, aby zvážila:<ul><li>Pokud předáte formát languagecode-countrycode, použije se pouze část formátu languagecode.</li><li>Pokud jazyk není v předchozím seznamu, rozdělená dovednost přeruší text na hranicích znaků.</li><li>Poskytnutí kódu jazyka je užitečné, aby se zabránilo řezání slovo na polovinu pro jazyky, které nejsou prázdné znaky, jako je čínština, japonština a korejština.</li><li>Pokud neznáte jazyk (tj. je třeba rozdělit text pro vstup do [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), výchozí angličtina (en) by měla být dostatečná. </li></ul>  |


## <a name="skill-inputs"></a>Vstupy dovedností

| Název parametru       | Popis      |
|----------------------|------------------|
| text  | Text rozdělit na podřetězec. |
| languageCode  | (Nepovinné) Kód jazyka pro dokument. Pokud neznáte jazyk (tj. je třeba rozdělit text pro vstup do [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), je bezpečné odebrat tento vstup.  |

## <a name="skill-outputs"></a>Výstupy dovedností 

| Název parametru     | Popis |
|--------------------|-------------|
| textové položky | Pole podřetězců, které byly extrahovány. |


##  <a name="sample-definition"></a>Definice vzorku

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

##  <a name="sample-input"></a>Ukázkový vstup

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
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

##  <a name="sample-output"></a>Vzorový výstup

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

## <a name="error-cases"></a>Chybové případy
Pokud jazyk není podporován, je generováno upozornění a text je rozdělen na hranici znaků.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
