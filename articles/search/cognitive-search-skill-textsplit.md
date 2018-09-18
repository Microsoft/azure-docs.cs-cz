---
title: Text rozdělit dovednosti kognitivního vyhledávání (Azure Search) | Dokumentace Microsoftu
description: Rozdělte text do bloků dat nebo stránek textu na základě délky v rozšíření kanálu služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 583d2ac5a8ac4c236612cdfe78595da1812c56fa
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730762"
---
#   <a name="text-split-cognitive-skill"></a>Text rozdělit kognitivních dovedností

**Rozdělení textu** dovednosti rozdělí text na bloky textu. Můžete určit, zda chcete provést přerušení textu do věty nebo do stránky určité délky. Tato dovednosti je obzvláště užitečné, pokud existují maximální text požadavky na délku ve směru server-klient další dovednosti. 

> [!NOTE]
> Cognitive Search je ve veřejné verzi Preview. Spuštění dovednosti a extrakce image a normalizace se momentálně nabízí zdarma. Později cenách za tyto funkce bude oznámena. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parametry dovedností

Parametry rozlišují malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| textSplitMode      | "Výraz stránky" nebo "věty" | 
| maximumPageLength | Pokud textSplitMode nastavená na výraz "stránky", to se vztahuje na délku maximální stránky naměřenou podle `String.Length`. Minimální hodnota je 100. | 
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
