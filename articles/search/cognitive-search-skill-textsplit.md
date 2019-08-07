---
title: Dovednost pro vyhledávání rozpoznávání textu rozdělení – Azure Search
description: Rozdělte text na bloky dat nebo na stránky textu na základě délky v kanálu pro rozšíření Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 8fb7ff51507212dfb162c09fcee469d6f154f3c3
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840896"
---
#   <a name="text-split-cognitive-skill"></a>Dovednosti při rozpoznávání rozdělení textu

Dovednost **rozdělení textu** rozdělí text na bloky textu. Můžete určit, zda chcete přerušit text na věty nebo na stránky určité délky. Tato dovednost je užitečná hlavně v případě, že jsou k dispozici maximální požadavky na délku textu v jiných dovednostech. 

> [!NOTE]
> Tato dovednost není vázaná na rozhraní Cognitive Services API a neúčtují se za jejich použití. K dispozici byste ale měli i [prostředek Cognitive Services](cognitive-search-attach-cognitive-services.md), abyste mohli přepsat možnost bezplatného prostředku, která omezuje na malý počet denních rozšíření za den.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parametry dovednosti

V parametrech jsou rozlišována malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| textSplitMode      | Buď "stránky" nebo "věty" | 
| maximumPageLength | Pokud je textSplitMode nastavené na "stránky", vztahuje se na maximální délka stránky měřená podle `String.Length`. Minimální hodnota je 100.  Pokud je textSplitMode nastavené na "stránky", algoritmus se pokusí rozdělit text na bloky, které mají velikost nejvíce "maximumPageLength". V takovém případě bude algoritmus nejlépe přerušit větu na hranici věty, takže velikost bloku dat může být mírně menší než "maximumPageLength". | 
| defaultLanguageCode   | volitelné Jeden z následujících kódů jazyka: `da, de, en, es, fi, fr, it, ko, pt`. Výchozí hodnota je angličtina (EN). Několik věcí, které je potřeba vzít v úvahu:<ul><li>Pokud předáte formát LanguageCode-CountryCode, použije se pouze část formátu LanguageCode.</li><li>Pokud jazyk není v předchozím seznamu, rozdělená dovednost rozdělí text na hranice znaků.</li><li>Poskytnutí kódu jazyka je užitečné, abyste se vyhnuli vyjmutí slova na polovinu pro neprostorové jazyky, jako jsou čínština, japonština a korejština.</li></ul>  |


## <a name="skill-inputs"></a>Vstupy dovedností

| Název parametru       | Popis      |
|----------------------|------------------|
| text  | Text, který se má rozdělit na dílčí řetězec |
| languageCode  | Volitelné Kód jazyka pro dokument  |

## <a name="skill-outputs"></a>Výstupy dovedností 

| Název parametru     | Popis |
|--------------------|-------------|
| textItems | Pole podřetězců, které byly extrahovány. |


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

## <a name="error-cases"></a>Chybové případy
Pokud jazyk není podporován, je vygenerováno upozornění a text bude rozdělen na hranice znaků.

## <a name="see-also"></a>Viz také:

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
