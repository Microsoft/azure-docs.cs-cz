---
title: Vnímání znalostí rozpoznávání entit
titleSuffix: Azure Cognitive Search
description: Extrakce různých typů entit z textu v kanálu rozšíření v Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 3300d68c6356cb7cdd56a9ad79eb0ac011942eb2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547896"
---
#    <a name="entity-recognition-cognitive-skill"></a>Vnímání znalostí rozpoznávání entit

Dovednost pro **rozpoznávání entit** extrahuje z textu entity různých typů. Tato dovednost používá v Cognitive Services modely strojového učení, které poskytuje [Analýza textu](../cognitive-services/text-analytics/overview.md) .

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů ve službě Azure Kognitivní hledání. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci imagí jsou popsané na [stránce s cenami za Azure kognitivní hledání](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. text. EntityRecognitionSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu musí být 50 000 znaků měřených podle [`String.Length`](/dotnet/api/system.string.length) . Pokud potřebujete data před odesláním do extraktoru klíčové fráze rozdělit, zvažte použití [dovednosti rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovednosti

V parametrech jsou rozlišována malá a velká písmena a jsou volitelná.

| Název parametru     | Description |
|--------------------|-------------|
| `categories`    | Pole kategorií, které mají být extrahovány.  Možné typy kategorií: `"Person"` , `"Location"` , `"Organization"` , `"Quantity"` , `"Datetime"` , `"URL"` , `"Email"` . Pokud není zadána žádná kategorie, jsou vráceny všechny typy.|
| `defaultLanguageCode` |    Kód jazyka vstupního textu Podporují se tyto jazyky: `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans` . Ne všechny kategorie entit jsou podporovány pro všechny jazyky; Viz poznámka níže.|
| `minimumPrecision` | Hodnota v rozsahu 0 až 1. Pokud je výsledek spolehlivosti (ve `namedEntities` výstupu) nižší než tato hodnota, entita se nevrátí. Výchozí hodnota je 0. |
| `includeTypelessEntities` | Nastavte na, `true` Pokud chcete rozpoznat dobře známé entity, které se nevejdou do aktuálních kategorií. Rozpoznané entity jsou vráceny v `entities` poli se složitým výstupem. Například "Windows 10" je dobře známá entita (produkt), ale vzhledem k tomu, že "Products" není podporovanou kategorií, bude tato entita obsažena v poli výstup entit. Výchozí hodnota je `false` |


## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Description                   |
|---------------|-------------------------------|
| `languageCode`    | Nepovinný parametr. Výchozí je `"en"`.  |
| `text`          | Text, který se má analyzovat          |

## <a name="skill-outputs"></a>Výstupy dovedností

> [!NOTE]
> Ne všechny kategorie entit jsou podporovány pro všechny jazyky. `"Person"` `"Location"` `"Organization"` Typy kategorií entit, a jsou podporovány pro úplný seznam jazyků uvedených výše. Jenom _de_, _EN_, _ES_, _fr_ a _zh-Hans_ podporují extrakci `"Quantity"` typů, `"Datetime"` , `"URL"` a `"Email"` . Další informace najdete v tématu [Podpora jazyků a oblastí pro rozhraní API pro analýzu textu](../cognitive-services/text-analytics/language-support.md).  

| Název výstupu      | Description                   |
|---------------|-------------------------------|
| `persons`       | Pole řetězců, kde každý řetězec představuje jméno osoby. |
| `locations`  | Pole řetězců, kde každý řetězec představuje umístění. |
| `organizations`  | Pole řetězců, kde každý řetězec představuje organizaci. |
| `quantities`  | Pole řetězců, kde každý řetězec představuje množství. |
| `dateTimes`  | Pole řetězců, kde každý řetězec představuje hodnotu DateTime (jak je zobrazeno v textu). |
| `urls` | Pole řetězců, kde každý řetězec představuje adresu URL |
| `emails` | Pole řetězců, kde každý řetězec představuje e-mail |
| `namedEntities` | Pole komplexních typů, které obsahují následující pole: <ul><li>category</li> <li>Value (skutečný název entity)</li><li>posun (umístění, kde byl nalezen v textu)</li><li>důvěra (vyšší hodnota znamená, že se jedná o skutečnou entitu)</li></ul> |
| `entities` | Pole komplexních typů, které obsahují formátované informace o entitách extrahovaných z textu, s následujícími poli <ul><li> název (skutečný název entity. Toto představuje "normalizovaný" tvar.</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (odkaz na stránku Wikipedii pro entitu)</li><li>bingId</li><li>typ (kategorie rozpoznané entity)</li><li>Podtyp (k dispozici pouze pro určité kategorie, poskytuje podrobnější zobrazení typu entity)</li><li> odpovídá (komplexní kolekce, která obsahuje)<ul><li>text (nezpracovaný text pro entitu)</li><li>posun (umístění, kde byl nalezen)</li><li>Délka (délka textu nezpracované entity)</li></ul></li></ul> |

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
##    <a name="sample-input"></a>Ukázkový vstup

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

Všimněte si, že posuny vracené pro entity ve výstupu této dovednosti jsou přímo vráceny z [rozhraní API pro analýzu textu](../cognitive-services/text-analytics/overview.md), což znamená, že pokud je používáte k indexování do původního řetězce, měli byste použít třídu [StringInfo](/dotnet/api/system.globalization.stringinfo) v rozhraní .NET, aby bylo možné extrahovat správný obsah.  [Další podrobnosti najdete tady.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="warning-cases"></a>Případy upozornění
Pokud kód jazyka pro dokument není podporován, je vrácena upozornění a extrahovány žádné entity.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)