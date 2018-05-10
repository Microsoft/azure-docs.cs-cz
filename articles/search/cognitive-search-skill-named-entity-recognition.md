---
title: S názvem Entity rozpoznávání kognitivní vyhledávání odborností (Azure Search) | Microsoft Docs
description: Extrahuje pojmenované entity pro uživatele, umístění a organizace, z textu v kognitivní vyhledávání kanál služby Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 73ffcf5e2ced63fddaf0f5ef2ca7e72a7d94b966
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Pojmenované kognitivní odborností rozpoznávání Entity

**s názvem rozpoznávání Entity** odborností extrahuje pojmenované entity z textu. Dostupné entity zahrnují `person`, `location`, a `organization`.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="skill-parameters"></a>Parametry dovedností

Parametry jsou malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| Kategorie    | Pole kategorie, které by měla být rozbalena.  Kategorie možné typy: `"Person"`, `"Location"`, `"Organization"`. Pokud je k dispozici žádné kategorie, vrátí se všechny typy.|
|defaultLanguageCode |  Kód jazyka ze vstupního textu. Jsou podporovány následující jazyky: `ar, cs, da, de, en, es, fi, fr, he, hu, it, ko, pt-br, pt`|
| minimumPrecision  | Číslo mezi 0 a 1. Pokud přesnost je nižší než tato hodnota, nebudou vyhledány entity. Výchozí hodnota je 0.|

## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Popis                   |
|---------------|-------------------------------|
| languageCode  | Volitelné. Výchozí hodnota je `"en"`.  |
| Text          | Text k analýze.          |

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu     | Popis                   |
|---------------|-------------------------------|
| osob      | Pole řetězců, kde každý řetězec představuje jméno osoby. |
| Umístění  | Pole řetězců, kde každý řetězec představuje umístění. |
| Organizace  | Pole řetězců, kde každý řetězec představuje organizace. |
| entity | Pole komplexní typy. Každý komplexní typ zahrnuje následující pole: <ul><li>kategorie (`"person"`, `"organization"`, nebo `"location"`)</li> <li>hodnota (název skutečné entity)</li><li>posun (umístění, kde se nachází v textu)</li><li>spolehlivosti (hodnotu mezi 0 a 1, který představuje tuto jistota, že hodnota je skutečné entity)</li></ul> |

##  <a name="sample-definition"></a>Ukázka definice

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
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
             "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
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
      "data" : 
      {
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Seattle"],
        "organizations":["Microsoft Corporation"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 45,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 59,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Seattle",
            "offset": 5,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft Corporation",
            "offset": 120,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Případech chyb
Pokud zadejte kód nepodporovaný jazyk, nebo pokud obsah neodpovídá jazyk zadaný, je návratový chybu a jsou extrahovány žádné entity.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)