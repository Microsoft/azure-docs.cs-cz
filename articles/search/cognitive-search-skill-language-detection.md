---
title: Rozpoznávání rozpoznávání jazyka – dovednost
titleSuffix: Azure Cognitive Search
description: Vyhodnotí nestrukturovaný text a pro každý záznam vrátí identifikátor jazyka se skóre udávajícím sílu analýzy v kanálu rozšíření AI v Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 087989638193bb59001ed33c4ee253d61682d8bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935989"
---
#   <a name="language-detection-cognitive-skill"></a>Rozpoznávání rozpoznávání jazyka – dovednost

**Rozpoznávání jazyka** dovednost detekuje jazyk vstupního textu a oznamuje každému dokumentu odeslanému na žádost jeden kód jazyka. Kód jazyka se spáruje se skóre, které indikuje sílu analýzy. Tato dovednost používá v Cognitive Services modely strojového učení, které poskytuje [Analýza textu](../cognitive-services/text-analytics/overview.md) .

Tato možnost je užitečná hlavně v případě, že potřebujete zadat jazyk textu jako vstup do jiných dovedností (například [Analýza mínění dovednosti](cognitive-search-skill-sentiment.md) nebo [dovednost rozdělení textu](cognitive-search-skill-textsplit.md)).

Rozpoznávání jazyka využívá knihovny pro zpracování v přirozeném jazyce Bingu, což překračuje počet [podporovaných jazyků a oblastí](../cognitive-services/text-analytics/language-support.md) uvedených analýza textu. Přesný seznam jazyků není publikovaný, ale obsahuje všechny široce mluvené jazyky, navíc varianty, dialekty a některé regionální a kulturní jazyky. Pokud máte obsah vyjádřený v méně často používaném jazyce, můžete [vyzkoušet rozhraní API pro rozpoznávání jazyka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) , abyste viděli, jestli vrátí kód. Odpověď pro jazyky, které nelze zjistit, je `unknown` .

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů ve službě Azure Kognitivní hledání. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci imagí jsou popsané na [stránce s cenami za Azure kognitivní hledání](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. text. LanguageDetectionSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu musí být 50 000 znaků měřených podle [`String.Length`](/dotnet/api/system.string.length) . Pokud potřebujete data před odesláním do odbornosti detekce jazyka rozdělit, můžete použít [dovednost rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Vstupy dovedností

U parametrů se rozlišují malá a velká písmena.

| Vstupy     | Description |
|--------------------|-------------|
| `text` | Text, který má být analyzován.|

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu    | Description |
|--------------------|-------------|
| `languageCode` | Kód jazyka ISO 6391 pro identifikovaný jazyk. Například "en". |
| `languageName` | Název jazyka Například "Angličtina". |
| `score` | Hodnota v rozsahu 0 až 1. Pravděpodobnost, že je jazyk správně identifikovaný. Skóre může být menší než 1, pokud má věta smíšené jazyky.  |

##  <a name="sample-definition"></a>Definice vzorku

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
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


## <a name="error-cases"></a>Chybové případy
Pokud je text vyjádřen v nepodporovaném jazyce, je vygenerována chyba a není vrácen žádný identifikátor jazyka.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)