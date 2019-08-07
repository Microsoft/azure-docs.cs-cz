---
title: Dovednost vyhledávání rozpoznávání entit – Azure Search
description: Extrakce různých typů entit z textu v kanálu vyhledávání Azure Searchho rozpoznávání.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 1328edfd9a049daf9244df992b55f4fb1585c8ba
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841078"
---
#    <a name="entity-recognition-cognitive-skill"></a>Vnímání znalostí rozpoznávání entit

Dovednost pro **rozpoznávání entit** extrahuje z textu entity různých typů. Tato dovednost používá v Cognitive Services modely strojového učení, které poskytuje [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) .

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů v Azure Search. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsány na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu musí být 50 000 znaků měřených podle [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Pokud potřebujete data před odesláním do extraktoru klíčové fráze rozdělit, zvažte použití [dovednosti rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovednosti

V parametrech jsou rozlišována malá a velká písmena a jsou volitelná.

| Název parametru     | Popis |
|--------------------|-------------|
| Kategorie    | Pole kategorií, které mají být extrahovány.  Možné typy kategorií: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, ,`"URL"`. `"Email"` Pokud není zadána žádná kategorie, jsou vráceny všechny typy.|
|defaultLanguageCode |  Kód jazyka vstupního textu Podporují se tyto jazyky:`de, en, es, fr, it`|
|minimumPrecision | Nepoužívané. Vyhrazeno pro budoucí použití. |
|includeTypelessEntities | Pokud text obsahuje dobře známou entitu, která je nastavená na hodnotu true, ale nedá se rozdělit do kategorií na jednu z podporovaných kategorií, vrátí se jako `"entities"` součást pole se složitým výstupem. 
Jedná se o entity, které jsou dobře známé, ale nejsou klasifikované jako součást aktuálních podporovaných "kategorií". Například "Windows 10" je dobře známá entita (produkt), ale "Products" ještě nejsou v kategoriích, které jsou dnes podporovány. Výchozí hodnota je`false` |


## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Popis                   |
|---------------|-------------------------------|
| languageCode  | Volitelné. Výchozí hodnota je `"en"`.  |
| text          | Text, který se má analyzovat          |

## <a name="skill-outputs"></a>Výstupy dovedností

> [!NOTE]
> Ne všechny kategorie entit jsou podporovány pro všechny jazyky. Pouze _Podpora EN_, _ES_ podporuje extrakci `"Datetime"`typů `"URL"` `"Email"` ,`"Quantity"`,,,.

| Název výstupu     | Popis                   |
|---------------|-------------------------------|
| obyvatel      | Pole řetězců, kde každý řetězec představuje jméno osoby. |
| locations  | Pole řetězců, kde každý řetězec představuje umístění. |
| organizations  | Pole řetězců, kde každý řetězec představuje organizaci. |
| množství  | Pole řetězců, kde každý řetězec představuje množství. |
| Typy DateTime  | Pole řetězců, kde každý řetězec představuje hodnotu DateTime (jak je zobrazeno v textu). |
| adrese | Pole řetězců, kde každý řetězec představuje adresu URL |
| e-maily | Pole řetězců, kde každý řetězec představuje e-mail |
| namedEntities | Pole komplexních typů, které obsahují následující pole: <ul><li>category</li> <li>Value (skutečný název entity)</li><li>posun (umístění, kde byl nalezen v textu)</li><li>jistota (nepoužívá se teď Bude nastaveno na hodnotu-1).</li></ul> |
| entity | Pole komplexních typů, které obsahují formátované informace o entitách extrahovaných z textu, s následujícími poli <ul><li> název (skutečný název entity. Toto představuje "normalizovaný" tvar.</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (odkaz na stránku Wikipedii pro entitu)</li><li>bingId</li><li>typ (kategorie rozpoznané entity)</li><li>Podtyp (k dispozici pouze pro určité kategorie, poskytuje podrobnější zobrazení typu entity)</li><li> odpovídá (komplexní kolekce, která obsahuje)<ul><li>text (nezpracovaný text pro entitu)</li><li>posun (umístění, kde byl nalezen)</li><li>Délka (délka textu nezpracované entity)</li></ul></li></ul> |

##  <a name="sample-definition"></a>Definice vzorku

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
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
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##  <a name="sample-input"></a>Vzorový vstup

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
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
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": -1
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Chybové případy
Pokud kód jazyka pro dokument není podporován, je vrácena chyba a nejsou extrahovány žádné entity.

## <a name="see-also"></a>Viz také:

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
