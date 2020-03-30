---
title: Kognitivní dovednosti překladu textu
titleSuffix: Azure Cognitive Search
description: Vyhodnotí text a pro každý záznam vrátí text přeložený do zadaného cílového jazyka v kanálu obohacení AI v Azure Cognitive Search.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5089174fcfd5a97128c1f789b818243243a5282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460772"
---
#   <a name="text-translation-cognitive-skill"></a>Kognitivní dovednosti překladu textu

Dovednost **Překlad textu** vyhodnotí text a pro každý záznam vrátí text přeložený do zadaného cílového jazyka. Tato dovednost používá [překladač text rozhraní API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) k dispozici v Cognitive Services.

Tato možnost je užitečná, pokud očekáváte, že vaše dokumenty nemusí být všechny v jednom jazyce, v takovém případě můžete normalizovat text do jednoho jazyka před indexací pro vyhledávání překladem.  Je také užitečné pro případy použití lokalizace, kde můžete chtít mít kopie stejného textu k dispozici ve více jazycích.

[Translator Text API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) je neregionální kognitivní služba, což znamená, že vaše data není zaručeno, že zůstane ve stejné oblasti jako azure kognitivní vyhledávání nebo připojené kognitivní služby prostředku.

> [!NOTE]
> Při rozšiřování oboru zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI budete muset [připojit fakturovatelný prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md). Poplatky narůstají při volání API ve službách Cognitive Services a pro extrakci image jako součást fáze prolomení dokumentů v Azure Cognitive Search. Za extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Provádění vestavěných dovedností se účtuje za stávající [cenu průběžných plateb služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsané na [stránce s cenami Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="odatatype"></a>@odata.type  
Microsoft.skills.Text.PřekladSkill

## <a name="data-limits"></a>Omezení dat
Maximální velikost záznamu by měla být 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)znaků měřená písmenem . Pokud potřebujete rozdělit data před odesláním do dovednosti překladu textu, zvažte použití [dovednosti Rozdělení textu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Vstupy                | Popis |
|---------------------|-------------|
| defaultToLanguageCode | (Povinné) Kód jazyka, do kterého se mají překládat dokumenty pro dokumenty, které explicitně neurčují jazyk to. <br/> Viz [Úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| defaultFromLanguageCode | (Nepovinné) Kód jazyka pro překlad dokumentů z dokumentů, které explicitně neurčují jazyk od.  Pokud není zadán defaultFromLanguageCode, automatické zjišťování jazyka poskytované překladač text api bude použit k určení od jazyka. <br/> Viz [Úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| navrhlFrom | (Nepovinné) Kód jazyka pro překlad dokumentů z, kdy nejsou k dispozici vstup fromLanguageCode ani výchozí parametrFromLanguageCode a automatická detekce jazyka je neúspěšná.  Pokud není zadán jazyk suggestedFrom, angličtina (en) bude použita jako jazyk suggestedFrom. <br/> Viz [Úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |

## <a name="skill-inputs"></a>Vstupy dovedností

| Vstupní název     | Popis |
|--------------------|-------------|
| text | Text, který má být přeložen.|
| toLanguageCode    | Řetězec označující jazyk, do který by měl být text přeložen. Pokud tento vstup není zadán, defaultToLanguageCode se použije k překladu textu. <br/>Zobrazit [úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| fromLanguageCode  | Řetězec označující aktuální jazyk textu. Pokud tento parametr není zadán, defaultFromLanguageCode (nebo automatické rozpoznávání jazyka, pokud není k dispozici defaultFromLanguageCode) se použije k překladu textu. <br/>Zobrazit [úplný seznam podporovaných jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>Výstupy dovedností

| Název výstupu    | Popis |
|--------------------|-------------|
| přeloženýText | Řetězec výsledek překladu textu z přeloženéFromLanguageCode do translatedToLanguageCode.|
| přeloženoToLanguageCode  | Řetězec označující kód jazyka, do kterého byl text přeložen. Užitečné, pokud překládáte do více jazyků a chcete mít možnost sledovat, který text je který jazyk.|
| přeloženoFromLanguageCode    | Řetězec označující kód jazyka, ze kterého byl text přeložen. Užitečné, pokud jste se rozhodli pro možnost automatického rozpoznávání jazyka, protože tento výstup vám poskytne výsledek této detekce.|

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

##  <a name="sample-input"></a>Vstup vzorku

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


## <a name="errors-and-warnings"></a>Chyby a varování
Pokud zadáte nepodporovaný kód jazyka pro jazyk od nebo do, je generována chyba a text není přeložen.
Pokud je text prázdný, zobrazí se upozornění.
Pokud je text větší než 50 000 znaků, bude přeloženo pouze prvních 50 000 znaků a bude vydáno upozornění.

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
