---
title: S názvem dovednosti kognitivního vyhledávání rozpoznávání entit (Azure Search) | Dokumentace Microsoftu
description: Extrahujte pojmenované entity pro uživatele, umístění a organizace z textu v kanálu služby Azure Search kognitivního vyhledávání.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: luisca
ms.openlocfilehash: f9ff3f66f3a73fbaf1a4c2ca280c85f4bde65444
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442025"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Pojmenované rozpoznávání entit kognitivních dovedností

**Rozpoznávání entit s názvem** dovednosti z textu extrahuje pojmenované entity. Dostupné entity zahrnují `person`, `location` a `organization`.

> [!NOTE]
> <ul>
> <li>Cognitive Search je ve veřejné verzi Preview. Funkce spuštění sady dovedností, extrakce a normalizace image jsou v současnosti nabízené zdarma. Ceny těchto funkcí oznámíme později. </li>
> <li> Dovednosti rozpoznávání pojmenovaných entit je považován za "zastaralé" a nebude podporovat oficiálně od 15 února 2019. Postupujte podle doporučení uvedených v <a href="cognitive-search-skill-deprecated.md">zastaralé kognitivní dovednosti vyhledávací</a> stránky pro migraci podporované dovedností</li>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu by měla být 50 000 znaků pohledu `String.Length`. Pokud je potřeba rozdělit data před odesláním k extrakci klíčových frází, zvažte použití [dovedností rozdělit textové](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovedností

Parametry rozlišují malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| categories    | Pole kategorií, které má být extrahován.  Typy možných kategorií: `"Person"`, `"Location"`, `"Organization"`. Pokud je k dispozici žádná kategorie, budou vráceny všechny typy.|
|defaultLanguageCode |  Kód jazyka vstupního textu. Jsou podporovány následující jazyky: `de, en, es, fr, it`|
| minimumPrecision  | Číslo mezi 0 a 1. Pokud přesnost je menší než tato hodnota, se vrátí entity. Výchozí hodnota je 0.|

## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Popis                   |
|---------------|-------------------------------|
| languageCode  | Volitelné. Výchozí hodnota je `"en"`.  |
| text          | Text, který se má analyzovat          |

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupního     | Popis                   |
|---------------|-------------------------------|
| osoby      | Pole řetězců, přičemž každý řetězec představuje jméno osoby. |
| umístění  | Pole řetězců, kde každý řetězec představuje umístění. |
| organizations  | Pole řetězců, přičemž každý řetězec představuje organizace. |
| entity | Pole komplexních typů. Každý komplexní typ obsahuje následující pole: <ul><li>kategorie (`"person"`, `"organization"`, nebo `"location"`)</li> <li>hodnota (název skutečné entity)</li><li>posun (umístění, kde se nachází v textu)</li><li>spolehlivosti (hodnotu mezi 0 a 1, který představuje tuto jistotu, že hodnota je skutečné entity)</li></ul> |

##  <a name="sample-definition"></a>Ukázková definice

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
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
##  <a name="sample-input"></a>Ukázkový vstup

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "This is the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
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
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Případy chyb
Pokud kód jazyka pro dokument není podporován, vrátí se chyba a jsou extrahovány žádné entity.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Dovednosti rozpoznávání entit](cognitive-search-skill-entity-recognition.md)
