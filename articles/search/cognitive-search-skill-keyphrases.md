---
title: Extrakce klíčových frází vnímání znalostí
titleSuffix: Azure Cognitive Search
description: Vyhodnotí nestrukturovaný text a pro každý záznam vrátí seznam klíčových slov v kanálu rozšíření AI v Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ddcd95356f9b70fec5a74f36f5b80e55ea56b477
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744015"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Extrakce klíčových frází vnímání znalostí

**Extrakce klíčových frází** dovednost vyhodnocuje nestrukturovaný text a pro každý záznam vrátí seznam klíčových frází. Tato dovednost používá v Cognitive Services modely strojového učení, které poskytuje [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) .

Tato funkce je užitečná v případě, že potřebujete rychle identifikovat hlavní body konverzace v záznamu. Například daný vstupní text "potravina byla Delicious a existovaly milované zaměstnance", služba vrátí "jídlo" a "dokonalé" personál ".

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů ve službě Azure Kognitivní hledání. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci imagí jsou popsané na [stránce s cenami za Azure kognitivní hledání](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. text. KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu musí být 50 000 znaků měřených podle [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length) . Pokud potřebujete data před odesláním do extraktoru klíčové fráze rozdělit, zvažte použití [dovednosti rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Vstupy                | Popis |
|---------------------|-------------|
| defaultLanguageCode | Volitelné Kód jazyka, který se má použít pro dokumenty, které nespecifikují jazyk explicitně.  Pokud není zadán výchozí kód jazyka, použije se jako výchozí kód jazyka angličtina (EN). <br/> [Úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)najdete v části. |
| maxKeyPhraseCount   | Volitelné Maximální počet klíčových frází, které mají být vyprodukovány. |

## <a name="skill-inputs"></a>Vstupy dovedností

| Vstup  | Popis |
|--------------------|-------------|
| text | Text, který má být analyzován.|
| languageCode  |  Řetězec, který označuje jazyk záznamů. Pokud tento parametr nezadáte, použije se k analýze záznamů výchozí kód jazyka. <br/>Zobrazit [úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

## <a name="skill-outputs"></a>Výstupy dovedností

| Výstup  | Popis |
|--------------------|-------------|
| keyPhrases | Seznam klíčových frází extrahovaných ze vstupního textu. Klíčové fráze jsou vraceny v pořadí podle důležitosti. |


##  <a name="sample-definition"></a>Definice vzorku

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
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

##  <a name="sample-input"></a>Vzorový vstup

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
Pokud zadáte nepodporovaný kód jazyka, je vygenerována chyba a nebudou extrahovány klíčové fráze.
Pokud je text prázdný, bude se vytvářet upozornění.
Pokud je text větší než 50 000 znaků, analyzují se pouze prvních 50 000 znaků a bude vydáno upozornění.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
