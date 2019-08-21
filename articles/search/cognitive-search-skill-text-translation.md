---
title: Dovednost vyhledávání rozpoznávání textu – Azure Search
description: Vyhodnotí text a pro každý záznam vrátí text přeložený do zadaného cílového jazyka v kanálu pro rozšíření Azure Search.
services: search
manager: briansmi
author: careyjmac
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: cmacdo
ms.subservice: cognitive-search
ms.openlocfilehash: 5f608ee058dd4e463253f4c4f5e410c515031e25
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638880"
---
#   <a name="text-translation-cognitive-skill"></a>Vnímání znalostí překladu textu

Dovednost **překladu textu** vyhodnocuje text a pro každý záznam vrátí text přeložený do zadaného cílového jazyka. Tato dovednost používá [Translator text API v 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) dostupné v Cognitive Services.

Tato možnost je užitečná, pokud očekáváte, že vaše dokumenty nebudou v jednom jazyce. v takovém případě můžete normalizovat text na jeden jazyk před indexováním pro hledání pomocí překladu.  Je také užitečné pro případy použití lokalizace, kde můžete chtít, aby kopie stejného textu byly k dispozici v několika jazycích.

[Translator text API v 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) je Neoblastní služba pro rozpoznávání, což znamená, že vaše data nejsou zaručená zůstat ve stejné oblasti jako vaše Azure Search nebo připojený Cognitive Services prostředek.

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů v Azure Search. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsány na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. text. TranslationSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu musí být 50 000 znaků měřených podle [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Pokud potřebujete data před odesláním do dovednosti překladu textu rozdělit, zvažte použití [dovednosti rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovednosti

V parametrech jsou rozlišována malá a velká písmena.

| Vstupy                | Popis |
|---------------------|-------------|
| defaultToLanguageCode | Požadovanou Kód jazyka, ve kterém se mají dokumenty překládat do pro dokumenty, které nespecifikují jazyk pro explicitně. <br/> [Úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)najdete v části. |
| defaultFromLanguageCode | Volitelné Kód jazyka, ze kterého se mají dokumenty překládat pro dokumenty, které nespecifikují jazyk z jazyka explicitně.  Pokud není zadán parametr defaultFromLanguageCode, použije se k určení jazyka z tohoto Translator Text API automatické zjišování jazyka, které poskytuje. <br/> [Úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)najdete v části. |
| suggestedFrom | Volitelné Kód jazyka pro překlad dokumentů, pokud není zadán vstupní fromLanguageCode ani parametr defaultFromLanguageCode a automatické rozpoznávání jazyka neproběhlo úspěšně.  Pokud není zadán jazyk suggestedFrom, použije se jako jazyk suggestedFrom angličtina (EN). <br/> [Úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)najdete v části. |

## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu     | Popis |
|--------------------|-------------|
| text | Text, který se má přeložit|
| toLanguageCode    | Řetězec označující jazyk, na který má být text přeložen. Pokud tento vstup není zadán, použije se k převodu textu defaultToLanguageCode. <br/>Zobrazit [úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| fromLanguageCode  | Řetězec označující aktuální jazyk textu. Pokud není tento parametr zadán, použije se k převodu textu defaultFromLanguageCode (nebo automatické rozpoznávání jazyka, pokud není k dispozici defaultFromLanguageCode). <br/>Zobrazit [úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu    | Popis |
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

##  <a name="sample-input"></a>Vzorový vstup

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

## <a name="see-also"></a>Viz také:

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
