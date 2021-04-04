---
title: Vnímání znalostí překladu textu
titleSuffix: Azure Cognitive Search
description: Vyhodnotí text a pro každý záznam vrátí text přeložený do zadaného cílového jazyka v kanálu rozšíření AI v Azure Kognitivní hledání.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0953d750ee8b59e9889512bb64cfd276a0bbeb53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654859"
---
#   <a name="text-translation-cognitive-skill"></a>Vnímání znalostí překladu textu

Dovednost **překladu textu** vyhodnocuje text a pro každý záznam vrátí text přeložený do zadaného cílového jazyka. Tato dovednost používá [Translator text API v 3.0](../cognitive-services/translator/reference/v3-0-translate.md) dostupné v Cognitive Services.

Tato možnost je užitečná, pokud očekáváte, že vaše dokumenty nebudou v jednom jazyce. v takovém případě můžete normalizovat text na jeden jazyk před indexováním pro hledání pomocí překladu.  Je také užitečné pro případy použití lokalizace, kde můžete chtít, aby kopie stejného textu byly k dispozici v několika jazycích.

[Translator text API v 3.0](../cognitive-services/translator/reference/v3-0-reference.md) je Neoblastní služba pro rozpoznávání, což znamená, že vaše data nejsou zaručená zůstat ve stejné oblasti jako Azure kognitivní hledání nebo připojený Cognitive Services prostředek.

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů ve službě Azure Kognitivní hledání. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci imagí jsou popsané na [stránce s cenami za Azure kognitivní hledání](https://azure.microsoft.com/pricing/details/search/).

## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. text. TranslationSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu musí být 50 000 znaků měřených podle [`String.Length`](/dotnet/api/system.string.length) . Pokud potřebujete data před odesláním do dovednosti překladu textu rozdělit, zvažte použití [dovednosti rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Vstupy | Description |
|---------------------|-------------|
| defaultToLanguageCode | Požadovanou Kód jazyka, ve kterém se mají dokumenty překládat do pro dokumenty, které nespecifikují jazyk pro explicitně. <br/> [Úplný seznam podporovaných jazyků](../cognitive-services/translator/language-support.md)najdete v části. |
| defaultFromLanguageCode | Volitelné Kód jazyka, ze kterého se mají dokumenty překládat pro dokumenty, které nespecifikují jazyk z jazyka explicitně.  Pokud není zadán parametr defaultFromLanguageCode, použije se k určení jazyka z tohoto Translator Text API automatické zjišování jazyka, které poskytuje. <br/> [Úplný seznam podporovaných jazyků](../cognitive-services/translator/language-support.md)najdete v části. |
| suggestedFrom | Volitelné Kód jazyka pro překlad dokumentů, pokud není zadán vstupní fromLanguageCode ani parametr defaultFromLanguageCode a automatické rozpoznávání jazyka neproběhlo úspěšně.  Pokud není zadán jazyk suggestedFrom, použije se jako jazyk suggestedFrom angličtina (EN). <br/> [Úplný seznam podporovaných jazyků](../cognitive-services/translator/language-support.md)najdete v části. |

## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu     | Description |
|--------------------|-------------|
| text | Text, který se má přeložit|
| toLanguageCode    | Řetězec označující jazyk, na který má být text přeložen. Pokud tento vstup není zadán, použije se k převodu textu defaultToLanguageCode. <br/>Zobrazit [úplný seznam podporovaných jazyků](../cognitive-services/translator/language-support.md)|
| fromLanguageCode  | Řetězec označující aktuální jazyk textu. Pokud není tento parametr zadán, použije se k převodu textu defaultFromLanguageCode (nebo automatické rozpoznávání jazyka, pokud není k dispozici defaultFromLanguageCode). <br/>Zobrazit [úplný seznam podporovaných jazyků](../cognitive-services/translator/language-support.md)|

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu    | Description |
|--------------------|-------------|
| translatedText | Výsledek řetězce převodu textu z translatedFromLanguageCode do translatedToLanguageCode.|
| translatedToLanguageCode  | Řetězec označující kód jazyka, na který byl text přeložen. Užitečné v případě, že překládáte do více jazyků a chcete mít přehled o tom, jaký text má jazyk.|
| translatedFromLanguageCode    | Řetězec označující kód jazyka, ze kterého byl text přeložen. To je užitečné, pokud jste zvolili možnost automatického zjišování jazyka, protože tento výstup vám poskytne výsledek této detekce.|

##  <a name="sample-definition"></a>Definice vzorku

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
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
          "text": "We hold these truths to be self-evident, that all men are created equal."
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
}
```


##  <a name="sample-output"></a>Ukázkový výstup

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Chyby a upozornění
Pokud zadáte nepodporovaný kód jazyka pro jazyk z nebo do, je vygenerována chyba a text není přeložen.
Pokud je text prázdný, bude se vytvářet upozornění.
Pokud je text větší než 50 000 znaků, bude přeložen pouze prvních 50 000 znaků a bude vydáno upozornění.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)