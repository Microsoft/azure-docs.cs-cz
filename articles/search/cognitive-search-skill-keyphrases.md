---
title: Klíč frázi extrakce kognitivní vyhledávání odborností (Azure Search) | Microsoft Docs
description: Vyhodnotí nestrukturovaných text a pro každý záznam, vrátí seznam klíčů frází obohacení kanál služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a12efaa020e626e4a10a0708c9b84d8fe125588c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33791032"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Klíč frázi extrakce kognitivní dovedností

**Klíč frázi extrakce** odborností vyhodnocuje nestrukturovaných text a pro každý záznam, vrátí seznam hodnot klíčů frází.

Tato možnost je užitečná, pokud potřebujete rychle identifikovat hlavní body čtených v záznamu. Například daného vstupního textu "jídlo byla chutný a nebyly k dispozici vynikající pracovníci", služba vrátí "jídlo" a "vynikající zaměstnanci".

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu by měla být 50 000 znaků měřený podle `String.Length`. Pokud potřebujete rozdělit vaše data před odesláním do Extraktor klíče frázi, zvažte použití [Text rozdělení odborností](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovedností

Parametry jsou malá a velká písmena.
| Vstupy                | Popis |
|---------------------|-------------|
| defaultLanguageCode | (Volitelné) Kód jazyka, který chcete použít pro dokumenty, které nejsou explicitně zadat jazyk.  Pokud není ve výchozím jazyk kódu zadaný, angličtina (en) se použije jako výchozí kód jazyka. <br/> V tématu [úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Volitelné) Maximální počet klíčů frází k vytvoření. |

## <a name="skill-inputs"></a>Vstupy dovedností
| Vstupy     | Popis |
|--------------------|-------------|
| Text | Text, který má být analyzován.|
| languageCode  |  Řetězec označující jazyk záznamy. Pokud není tento parametr zadán, kód výchozí jazyk se použije k analýze záznamy. <br/>V tématu [úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Ukázka definice

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
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
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Chyby a upozornění
Pokud zadáte zadání nepodporovaný jazyk kódu, je generována chyba a nejsou rozbalené klíče frází.
Pokud text je prázdná, bude možné vytvořit upozornění.
Pokud text je delší než 50 000 znaků, analyzuje pouze první 50 000 znaků a objeví se upozornění.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)