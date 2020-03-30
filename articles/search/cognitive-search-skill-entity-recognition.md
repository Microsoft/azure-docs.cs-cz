---
title: Kognitivní dovednosti rozpoznávání entit
titleSuffix: Azure Cognitive Search
description: Extrahujte různé typy entit z textu v kanálu obohacení v Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6ef5952b6413563b2c2e16ff2218f709b414fb84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297810"
---
#    <a name="entity-recognition-cognitive-skill"></a>Kognitivní dovednosti rozpoznávání entit

Dovednost **Rozpoznávání entit** extrahuje entity různých typů z textu. Tato dovednost používá modely strojového učení poskytované [textovou analýzou](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) ve službách Cognitive Services.

> [!NOTE]
> Při rozšiřování oboru zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI budete muset [připojit fakturovatelný prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md). Poplatky narůstají při volání API ve službách Cognitive Services a pro extrakci image jako součást fáze prolomení dokumentů v Azure Cognitive Search. Za extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Provádění vestavěných dovedností se účtuje za stávající [cenu průběžných plateb služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsané na [stránce s cenami Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu by měla být 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)znaků měřená písmenem . Pokud potřebujete rozdělit data před odesláním do klíčové fráze extraktoru, zvažte použití [dovednosti Rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovednosti

Parametry rozlišují malá a velká písmena a všechny jsou volitelné.

| Název parametru     | Popis |
|--------------------|-------------|
| categories    | Pole kategorií, které by měly být extrahovány.  Možné `"Person"`typy kategorií: `"Location"` `"Organization"`, `"Quantity"` `"Datetime"`, `"URL"` `"Email"`, , , , . Pokud není k dispozici žádná kategorie, jsou vráceny všechny typy.|
|defaultLanguageCode |    Kód jazyka vstupního textu. Podporovány jsou následující `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans`jazyky: . Ne všechny kategorie entit jsou podporovány pro všechny jazyky; viz poznámka níže.|
|minimumPřesnost | Hodnota mezi 0 a 1. Pokud je skóre spolehlivosti (ve `namedEntities` výstupu) nižší než tato hodnota, entita není vrácena. Výchozí hodnota je 0. |
|includeTypelessEntities | Nastavte `true` na, pokud chcete rozpoznat známé entity, které neodpovídají aktuálním kategoriím. Rozpoznané entity jsou `entities` vráceny ve složitém výstupním poli. Například "Windows 10" je dobře známá entita (produkt), ale protože "Produkty" není podporovaná kategorie, tato entita by byla zahrnuta do výstupního pole entit. Výchozí hodnota je`false` |


## <a name="skill-inputs"></a>Vstupy dovedností

| Vstupní název      | Popis                   |
|---------------|-------------------------------|
| languageCode    | Nepovinný parametr. Výchozí je `"en"`.  |
| text          | Text k analýze.          |

## <a name="skill-outputs"></a>Výstupy dovedností

> [!NOTE]
> Ne všechny kategorie entit jsou podporovány pro všechny jazyky. Typy `"Person"` `"Location"`kategorií `"Organization"` , a entity jsou podporovány pro úplný seznam výše uvedených jazyků. Extrakci `"Quantity"`, `"Datetime"`, `"URL"`a `"Email"` typů podporují pouze _de_, _en_, _es_, _fr_a _zh-hans._ Další informace naleznete v [tématu Podpora jazyka a oblasti pro rozhraní API pro analýzu textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support).  

| Název výstupu      | Popis                   |
|---------------|-------------------------------|
| Osob       | Pole řetězců, kde každý řetězec představuje jméno osoby. |
| Umístění  | Pole řetězců, kde každý řetězec představuje umístění. |
| organizations  | Pole řetězců, kde každý řetězec představuje organizaci. |
| Množství  | Pole řetězců, kde každý řetězec představuje množství. |
| dateTimes  | Pole řetězců, kde každý řetězec představuje datetime (jak se zobrazí v textu) hodnotu. |
| Adresy url | Pole řetězců, kde každý řetězec představuje adresu URL |
| E-maily | Pole řetězců, kde každý řetězec představuje e-mail |
| pojmenované entity | Pole komplexních typů, které obsahuje následující pole: <ul><li>category</li> <li>hodnota (Název skutečné entity)</li><li>posun (Umístění, kde byl nalezen v textu)</li><li>důvěra (Vyšší hodnota znamená, že je to spíše být skutečnou entitou)</li></ul> |
| Entity | Pole komplexních typů, které obsahuje bohaté informace o entitách extrahovaných z textu, s následujícími poli <ul><li> název (název skutečné entity. To představuje "normalizovaný" formulář)</li><li> wikipediaId</li><li>wikipediaJazyk</li><li>wikipediaUrl (odkaz na stránku Wikipedie pro entitu)</li><li>bingId</li><li>typu (kategorie účetní jednotky, která byla vykázána)</li><li>subType (k dispozici pouze pro určité kategorie, to dává podrobnější zobrazení typu entity)</li><li> shody (komplexní kolekce, která obsahuje)<ul><li>text (nezpracovaný text pro entitu)</li><li>offset (místo, kde byl nalezen)</li><li>délka (délka textu nezpracované entity)</li></ul></li></ul> |

##    <a name="sample-definition"></a>Definice vzorku

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
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
##    <a name="sample-input"></a>Vstup vzorku

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

##    <a name="sample-output"></a>Ukázkový výstup

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
            "confidence": 0.98
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

Všimněte si, že posuny vrácené pro entity ve výstupu této dovednosti jsou přímo vráceny z [rozhraní API analýzy textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), což znamená, že pokud je používáte k indexování do původního řetězce, měli byste použít třídu [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) v rozhraní .NET, abyste extrahovali správný obsah.  [Více informací naleznete zde.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-cases"></a>Chybové případy
Pokud kód jazyka pro dokument není podporován, je vrácena chyba a nejsou extrahovány žádné entity.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
