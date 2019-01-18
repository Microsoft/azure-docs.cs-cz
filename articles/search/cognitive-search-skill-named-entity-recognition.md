---
title: S názvem dovednosti kognitivního vyhledávání entit – Azure Search
description: Extrahujte pojmenované entity pro uživatele, umístění a organizace z textu v kanálu služby Azure Search kognitivního vyhledávání.
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
ms.openlocfilehash: 55244544ff50dbaf6c22fe0f4d55257eb887eaba
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389649"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Pojmenované rozpoznávání entit kognitivních dovedností

**Rozpoznávání entit s názvem** dovednosti z textu extrahuje pojmenované entity. Dostupné entity zahrnují `person`, `location` a `organization`.

> [!IMPORTANT]
> Zastaralé dovednosti rozpoznávání pojmenovaných entit nahrazuje [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). Podpora zastaví na 15 února 2019. Postupujte podle doporučení v [zastaralé kognitivní dovednosti hledání](cognitive-search-skill-deprecated.md) migrovat do podporovaných dovedností.

> [!NOTE]
> Od 21. prosince 2018 můžete [připojit prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md) s dovednosti Azure Search. To umožňuje spouštění poplatků za využití jeho dovedností. K tomuto datu také začali účtovat pro extrakci image jako součást fáze hádání dokumentu. Extrakce textu z dokumentů dál nabízet bez dalších poplatků.
>
> Provádění předdefinované dovednosti je poplatek za služby Cognitive Services, účtovat stávající [platit jako můžete přejít cena](https://azure.microsoft.com/pricing/details/cognitive-services/) . Ceny extrakce Image je poplatek za Azure Search, aktuálně účtovat ceny verze preview, jak je popsáno na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). 

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
