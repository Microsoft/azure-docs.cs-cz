---
title: Text rozdělit kognitivního vyhledávání dovednosti – Azure Search
description: Rozdělte text do bloků dat nebo stránek textu na základě délky v rozšíření kanálu služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: d5a8fa61307c278a77b33868a711970850a06f3e
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54410043"
---
#   <a name="text-split-cognitive-skill"></a>Text rozdělit kognitivních dovedností

**Rozdělení textu** dovednosti rozdělí text na bloky textu. Můžete určit, zda chcete provést přerušení textu do věty nebo do stránky určité délky. Tato dovednosti je obzvláště užitečné, pokud existují maximální text požadavky na délku ve směru server-klient další dovednosti. 

> [!NOTE]
> Tato dovednosti není vázán na API služeb Cognitive Services a se vám neúčtují poplatky k jeho používání. Měli stále [připojit prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md), ale k přepsání **Free** resource – možnost, která omezuje vám malý počet denních obohacení za den.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parametry dovedností

Parametry rozlišují malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| textSplitMode      | "Výraz stránky" nebo "věty" | 
| maximumPageLength | Pokud textSplitMode nastavená na výraz "stránky", to se vztahuje na délku maximální stránky naměřenou podle `String.Length`. Minimální hodnota je 100.  Pokud textSplitMode nastavená na výraz "stránky", se pokusí algoritmus rozdělit do bloků dat, které jsou maximálně "maximumPageLength" velikost textu. V takovém případě provede algoritmus je nejlepší k rozdělení věty na hranici věty tak velikost bloku dat může být o něco menší než "maximumPageLength". | 
| defaultLanguageCode   | (volitelné) Jeden z následujících kódů jazyka: `da, de, en, es, fi, fr, it, ko, pt`. Výchozí hodnota je angličtina (en). Vezměte v úvahu několik věcí:<ul><li>Pokud předáte formátu languagecode countrycode, se používá jenom část languagecode formátu.</li><li>Pokud jazyk není v předchozím seznamu, přestane fungovat dovednosti rozdělit na znak hranice.</li><li>Poskytuje kód jazyka je vhodné vyhnout cutting slova na polovinu jiný než mezera jazyků, jako jsou čínštiny, japonštiny a korejštiny.</li></ul>  |


## <a name="skill-inputs"></a>Vstupy dovedností

| Název parametru       | Popis      |
|----------------------|------------------|
| text  | Text, který rozdělit na dílčí řetězec. |
| languageCode  | (Volitelné) Kód jazyka pro dokument.  |

## <a name="skill-outputs"></a>Výstupy dovedností 

| Název parametru     | Popis |
|--------------------|-------------|
| textItems | Pole dílčích řetězců, které byly extrahovány. |


##  <a name="sample-definition"></a>Ukázková definice

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

## <a name="error-cases"></a>Případy chyb
Pokud jazyk není podporován, je vygenerováno upozornění a text je rozdělený na znak hranice.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
