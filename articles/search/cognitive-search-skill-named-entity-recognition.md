---
title: Vnímání znalostí rozpoznávání pojmenovaných entit
titleSuffix: Azure Cognitive Search
description: Extrahujte pojmenované entity pro osobu, umístění a organizaci z textu v kanálu rozšíření AI v Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e598f16c6b441cf986c7ac82d67c037f75be8982
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547437"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Vnímání znalostí rozpoznávání pojmenovaných entit

Dovednost pro **rozpoznávání pojmenovaných entit** extrahuje pojmenované entity z textu. Dostupné entity zahrnují typy `person` , `location` a `organization` .

> [!IMPORTANT]
> Dovednost pro rozpoznávání pojmenovaných entit je teď přerušená [Microsoft. dovednosti. text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). Podpora se zastavila 15. února 2019 a rozhraní API se z produktu odebralo 2. května 2019. Pokud chcete migrovat na podporovanou dovednost, postupujte podle doporučení v části [nepoužívané poznatky pro vyhledávání v rozpoznávání](cognitive-search-skill-deprecated.md) .

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů ve službě Azure Kognitivní hledání. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci imagí jsou popsané na [stránce s cenami za Azure kognitivní hledání](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. text. NamedEntityRecognitionSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu musí být 50 000 znaků měřených podle [`String.Length`](/dotnet/api/system.string.length) . Pokud potřebujete data před odesláním do extraktoru klíčové fráze rozdělit, zvažte použití [dovednosti rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Název parametru     | Description |
|--------------------|-------------|
| categories    | Pole kategorií, které mají být extrahovány.  Možné typy kategorií: `"Person"` , `"Location"` , `"Organization"` . Pokud není zadána žádná kategorie, jsou vráceny všechny typy.|
|defaultLanguageCode |  Kód jazyka vstupního textu Podporují se tyto jazyky: `de, en, es, fr, it`|
| minimumPrecision  | Číslo mezi 0 a 1. Pokud je přesnost nižší než tato hodnota, entita se nevrátí. Výchozí hodnota je 0.|

## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Description                   |
|---------------|-------------------------------|
| languageCode  | Nepovinný parametr. Výchozí je `"en"`.  |
| text          | Text, který se má analyzovat          |

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu     | Description                   |
|---------------|-------------------------------|
| obyvatel      | Pole řetězců, kde každý řetězec představuje jméno osoby. |
| polohy  | Pole řetězců, kde každý řetězec představuje umístění. |
| organizations  | Pole řetězců, kde každý řetězec představuje organizaci. |
| podnikům | Pole komplexních typů. Každý komplexní typ obsahuje následující pole: <ul><li>kategorie ( `"person"` , `"organization"` nebo `"location"` )</li> <li>Value (skutečný název entity)</li><li>posun (umístění, kde byl nalezen v textu)</li><li>důvěra (hodnota mezi 0 a 1, která představuje jistotu, že hodnota je skutečnou entitou)</li></ul> |

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
##  <a name="sample-input"></a>Ukázkový vstup

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


## <a name="warning-cases"></a>Případy upozornění
Pokud kód jazyka pro dokument není podporován, je vrácena upozornění a extrahovány žádné entity.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Dovednost pro rozpoznávání entit](cognitive-search-skill-entity-recognition.md)