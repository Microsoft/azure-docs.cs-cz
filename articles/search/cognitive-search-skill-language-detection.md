---
title: Dovednosti kognitivního vyhledávání detekce jazyka (Azure Search) | Dokumentace Microsoftu
description: Vyhodnotí nestrukturovaného textu a pro každý záznam, vrátí se skóre označující sílu analýzy v kanálu služby Azure Search obohacování identifikátor jazyka.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 2fd1c1ec0d2442afd6367e1d35af6f798dced2c7
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733274"
---
#   <a name="language-detection-cognitive-skill"></a>Kognitivní dovednosti detekce jazyka

Až 120 jazyků **rozpoznávání jazyka** dovednosti jazyk vstupního textu zjišťuje a vykazuje jeden jazyk kódu pro každý dokument podání žádosti. Kód jazyka je spárovaná s skóre označuje sílu analýzy.

Tato funkce je obzvláště užitečná při budete muset zadat jazyk textu jako vstup pro další dovednosti (například [analýzu subjektivního hodnocení dovednosti](cognitive-search-skill-sentiment.md) nebo [dovedností rozdělit textové](cognitive-search-skill-textsplit.md)).

> [!NOTE]
> Cognitive Search je ve veřejné verzi Preview. Spuštění dovednosti a extrakce image a normalizace se momentálně nabízí zdarma. Později cenách za tyto funkce bude oznámena. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu by měla být 50 000 znaků pohledu `String.Length`. Pokud je potřeba rozdělit data před odesláním analýza mínění, můžete použít [dovedností rozdělit textové](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Vstupy dovedností

Parametry rozlišují malá a velká písmena.

| Vstupy     | Popis |
|--------------------|-------------|
| text | Text, který má být analyzován.|

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupního    | Popis |
|--------------------|-------------|
| languageCode | Kód jazyka ISO 6391 pro jazyky identifikovat. Například "en". |
| LanguageName | Název jazyka. Například "Angličtina". |
| skóre | Hodnota mezi 0 a 1. Pravděpodobnost, že je správně identifikovat jazyk. Skóre může být nižší než 1, pokud věty se smíšenými jazyků.  |

##  <a name="sample-definition"></a>Ukázková definice

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

##  <a name="sample-input"></a>Ukázkový vstup

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


## <a name="error-cases"></a>Případy chyb
Pokud textu je vyjádřena v nepodporovaný jazyk, je vygenerována chyba a vrátí se žádný identifikátor jazyka.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
