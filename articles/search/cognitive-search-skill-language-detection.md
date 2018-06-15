---
title: Jazyk detekce kognitivní vyhledávání odborností (Azure Search) | Microsoft Docs
description: Vyhodnotí nestrukturovaných text a pro každý záznam, vrátí identifikátor jazyka se skóre označující sílu analýzy v obohacení kanál služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 338d89b47ea451efcf8300d4ac016a6946a95259
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33791053"
---
#   <a name="language-detection-cognitive-skill"></a>Jazyk detekce kognitivní dovedností

Až 120 jazyků **zjišťování jazyka** odborností jazyk je vstupního textu a sestavy jeden jazyk kódu pro každý dokument odeslaných v požadavku. Kód jazyka je spárován s skóre, která určuje, jaká síla analýzy.

Tato funkce je obzvláště užitečná při budete muset zadat jazyk textu jako vstup pro jiné dovednosti (například [odborností postojích Snalysis](cognitive-search-skill-sentiment.md) nebo [Text rozdělení odborností](cognitive-search-skill-textsplit.md)).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu by měla být 50 000 znaků měřený podle `String.Length`. Pokud potřebujete rozdělit vaše data před odesláním do analyzátor postojích, můžete použít [Text rozdělení odborností](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Vstupy dovedností

Parametry jsou malá a velká písmena.

| Vstupy     | Popis |
|--------------------|-------------|
| Text | Text, který má být analyzován.|

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu    | Popis |
|--------------------|-------------|
| languageCode | Kód jazyka ISO 6391 pro jazyk identifikovat. Například "en". |
| LanguageName | Název jazyka. Například "Angličtina". |
| Skóre | Hodnota mezi 0 a 1. Pravděpodobnost, že jazyk je identifikovány správně. Skóre může být nižší než 1, pokud se věta se smíšenými jazyky.  |

##  <a name="sample-definition"></a>Ukázka definice

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill ",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      }
    ]
```


##  <a name="sample-output"></a>Ukázkový výstup

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>Případech chyb
Pokud text je vyjádřena v nepodporovaný jazyk, je generována chyba a je vrácen žádný identifikátor jazyka.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)