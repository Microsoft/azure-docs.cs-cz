---
title: Kognitivní dovednost rozpoznávání pojmenovaných entit
titleSuffix: Azure Cognitive Search
description: Extrahujte pojmenované entity pro osobu, umístění a organizaci z textu v kanálu obohacení AI v Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 127155e492b556ce1ce02b67cf0b0846b99ebcd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791939"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Kognitivní dovednost rozpoznávání pojmenovaných entit

Dovednost **Rozpoznávání pojmenovaných entit** extrahuje pojmenované entity z textu. Mezi dostupné entity `person`patří `location` `organization`typy a .

> [!IMPORTANT]
> Pojmenovaná dovednost rozpoznávání entit je nyní ukončena nahrazena [microsoft.skills.text.entityrecognitionskill](cognitive-search-skill-entity-recognition.md). Podpora byla zastavena 15. února 2019 a rozhraní API bylo z produktu odstraněno 2. května 2019. Postupujte podle doporučení v [zastaralé kognitivní vyhledávání dovednosti](cognitive-search-skill-deprecated.md) migrovat na podporované dovednosti.

> [!NOTE]
> Při rozšiřování oboru zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI budete muset [připojit fakturovatelný prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md). Poplatky narůstají při volání API ve službách Cognitive Services a pro extrakci image jako součást fáze prolomení dokumentů v Azure Cognitive Search. Za extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Provádění vestavěných dovedností se účtuje za stávající [cenu průběžných plateb služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsané na [stránce s cenami Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu by měla být 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)znaků měřená písmenem . Pokud potřebujete rozdělit data před odesláním do klíčové fráze extraktoru, zvažte použití [dovednosti Rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Název parametru     | Popis |
|--------------------|-------------|
| categories    | Pole kategorií, které by měly být extrahovány.  Možné typy `"Person"`kategorií: `"Location"` `"Organization"`, , . Pokud není k dispozici žádná kategorie, jsou vráceny všechny typy.|
|defaultLanguageCode |  Kód jazyka vstupního textu. Podporovány jsou následující jazyky:`de, en, es, fr, it`|
| minimumPřesnost  | Číslo mezi 0 a 1. Pokud je přesnost nižší než tato hodnota, entita není vrácena. Výchozí hodnota je 0.|

## <a name="skill-inputs"></a>Vstupy dovedností

| Vstupní název      | Popis                   |
|---------------|-------------------------------|
| languageCode  | Nepovinný parametr. Výchozí je `"en"`.  |
| text          | Text k analýze.          |

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu     | Popis                   |
|---------------|-------------------------------|
| Osob      | Pole řetězců, kde každý řetězec představuje jméno osoby. |
| Umístění  | Pole řetězců, kde každý řetězec představuje umístění. |
| organizations  | Pole řetězců, kde každý řetězec představuje organizaci. |
| Entity | Pole komplexní ch typů. Každý komplexní typ obsahuje následující pole: <ul><li>kategorie (`"person"` `"organization"`, `"location"`, nebo )</li> <li>hodnota (název skutečné entity)</li><li>posun (Umístění, kde byl nalezen v textu)</li><li>důvěra (Hodnota mezi 0 a 1, která představuje tuto důvěru, že hodnota je skutečnou entitou)</li></ul> |

##  <a name="sample-definition"></a>Definice vzorku

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
##  <a name="sample-input"></a>Vstup vzorku

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
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


## <a name="error-cases"></a>Chybové případy
Pokud kód jazyka pro dokument není podporován, je vrácena chyba a nejsou extrahovány žádné entity.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Dovednost rozpoznávání entit](cognitive-search-skill-entity-recognition.md)
