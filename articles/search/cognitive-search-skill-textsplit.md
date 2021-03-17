---
title: Dovednosti při rozpoznávání rozdělení textu
titleSuffix: Azure Cognitive Search
description: Přerušit text na bloky dat nebo na stránky textu na základě délky kanálu rozšíření AI v Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 52aaeb01fef551eee350c6db662c2690ef7b3e78
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84981944"
---
# <a name="text-split-cognitive-skill"></a>Dovednosti při rozpoznávání rozdělení textu

Dovednost **rozdělení textu** rozdělí text na bloky textu. Můžete určit, zda chcete přerušit text na věty nebo na stránky určité délky. Tato dovednost je užitečná hlavně v případě, že jsou k dispozici maximální požadavky na délku textu v jiných dovednostech. 

> [!NOTE]
> Tato dovednost není vázaná na rozhraní Cognitive Services API a neúčtují se za jejich použití. K dispozici byste ale měli i [prostředek Cognitive Services](cognitive-search-attach-cognitive-services.md), abyste mohli přepsat možnost **bezplatného** prostředku, která omezuje na malý počet denních rozšíření za den.

## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. text. SplitSkill 

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Název parametru     | Description |
|--------------------|-------------|
| `textSplitMode`    | Buď "stránky" nebo "věty" | 
| `maximumPageLength` | Pokud je textSplitMode nastavené na "stránky", vztahuje se na maximální délka stránky měřená podle `String.Length` . Minimální hodnota je 300.  Pokud je textSplitMode nastavené na "stránky", algoritmus se pokusí rozdělit text na bloky, které mají velikost nejvíce "maximumPageLength". V takovém případě bude algoritmus nejlépe přerušit větu na hranici věty, takže velikost bloku dat může být mírně menší než "maximumPageLength". | 
| `defaultLanguageCode` | volitelné Jeden z následujících kódů jazyka: `da, de, en, es, fi, fr, it, ko, pt` . Výchozí hodnota je angličtina (EN). Několik věcí, které je potřeba vzít v úvahu:<ul><li>Pokud předáte formát LanguageCode-CountryCode, použije se pouze část formátu LanguageCode.</li><li>Pokud jazyk není v předchozím seznamu, rozdělená dovednost rozdělí text na hranice znaků.</li><li>Poskytnutí kódu jazyka je užitečné, abyste se vyhnuli vyjmutí slova na polovinu pro jiné než prázdné jazyky, jako jsou čínština, japonština a korejština.</li><li>Pokud neznáte jazyk (tj. je třeba rozdělit text na [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), měla by být stačit výchozí angličtina (EN). </li></ul>  |


## <a name="skill-inputs"></a>Vstupy dovedností

| Název parametru       | Description      |
|----------------------|------------------|
| `text`    | Text, který se má rozdělit na dílčí řetězec |
| `languageCode`    | Volitelné Kód jazyka pro dokument Pokud neznáte jazyk (například potřebujete rozdělit text na vstup do [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), je možné tento vstup bezpečně odebrat.  |

## <a name="skill-outputs"></a>Výstupy dovedností 

| Název parametru     | Description |
|--------------------|-------------|
| `textItems`   | Pole podřetězců, které byly extrahovány. |


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
Pokud jazyk není podporován, je vygenerováno upozornění a text bude rozdělen na hranice znaků.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
