---
title: Zjistit jazyk pomocí REST API pro analýzu textu | Dokumentace Microsoftu
description: Zjistit jazyk s využitím REST API pro analýzu textu z Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: e1adeb34cf999f471bb183e4d7de9c65427252bb
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986513"
---
# <a name="example-detect-language-with-text-analytics"></a>Příklad: Zjistit jazyk pomocí analýzy textu

[Rozpoznávání jazyka](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) funkce Azure REST API pro analýzu textu vyhodnotí textové zadání pro každý dokument a vrátí identifikátory jazyka se skóre, která určuje sílu analýzy.

Tato schopnost je užitečná pro úložiště obsahu, která shromažďují libovolné texty, u nichž není jazyk znám. Výsledky této analýzy můžete parsovat a určit, v kterém jazyce je vstupní dokument napsaný. Odpověď se vrátí taky skóre, které odráží důvěru modelu. Skóre hodnotu mezi 0 a 1.

Funkce detekce jazyka dokáže rozpoznat široké škály jazyků, proměnných typu variant, dialekty a některé jazyky oblasti nebo jazykové verze. Přesný seznam jazyků pro tuto funkci není publikována.

Pokud máte obsah vyjádřené v méně často používané jazyk, můžete zkusit funkce rozpoznávání jazyka zobrazíte, pokud vrací kód. Odpověď pro jazyky, které nelze zjistit `unknown`.

> [!TIP]
> Rozhraní text Analytics také poskytuje Dockeru založených na Linuxu image kontejneru pro rozpoznávání jazyka, tak, aby se [nainstalovat a spustit kontejner pro analýzu textu](text-analytics-how-to-install-containers.md) blízko datům.

## <a name="preparation"></a>Příprava

Dokumenty JSON musí mít v tomto formátu: ID a text.

Velikost dokumentu musí být v jednom dokumentu v části 5 120 znaků. Můžete mít až 1 000 položek (ID) na kolekci. Kolekce se posílá v textu žádosti. Následující příklad je příkladem obsah, který může odeslat k rozpoznání jazyka:

   ```
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },                
            {
                "id": "5",
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Krok 1: Struktura žádosti

Další informace o definici požadavku, najdete v části [volání rozhraní Text Analytics API](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvořte požadavek POST. Projděte si dokumentaci k rozhraní API pro tuto žádost, najdete v článku [rozhraní API pro detekci jazyka](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

+ Nastavte koncový bod HTTP pro rozpoznávání jazyka. Použít prostředek pro analýzu textu v Azure nebo instance [kontejneru pro analýzu textu](text-analytics-how-to-install-containers.md). Musí zahrnovat `/languages` prostředků: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`.

+ Nastavte hlavičku žádosti tak, aby obsahovala přístupový klíč pro operace analýzy textu. Další informace najdete v tématu [najít koncových bodů a přístupové klíče](text-analytics-how-to-access-key.md).

+ V textu požadavku zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použijte aplikaci [Postman](text-analytics-how-to-call-api.md) nebo otevřete **konzolu pro testování rozhraní API** v [dokumentaci](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) a vytvořte strukturu žádosti a pomocí příkazu POST ji odešlete do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslat žádost

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavky můžete odesílat za minutu a sekundu, najdete v článku [limity dat](../overview.md#data-limits) části v přehledu.

Nezapomeňte, že služba je bezstavová. Ve vašem účtu se neukládají žádná data. Výsledky se vrátí okamžitě v odpovědi.


## <a name="step-3-view-the-results"></a>Krok 3: Zobrazení výsledků

Všech požadavků POST zjištěné vlastnosti a vrátí odpověď ve formátu JSON pomocí identifikátorů dílčích modelů.

Výstup se vrátí okamžitě. Můžete Streamovat výsledky do aplikace, která přijímá JSON nebo uložte výstup do souboru v místním systému. Importujte výstup do aplikace, která slouží k řazení, hledání a manipulaci s daty.

Výsledky ukázkové žádosti by měly vypadat jako následující JSON. Všimněte si, že se jedná o jeden dokument s více položek. Výstup je v angličtině. Identifikátory jazyka zahrnují popisný název a kód jazyka ve formátu [ISO 639-1](https://www.iso.org/standard/22109.html).

Kladné skóre 1.0 vyjadřuje nejvyšší možnou úroveň spolehlivosti analýzy.



```
{
    "documents": [
        {
            "id": "1",
            "detectedLanguages": [
                {
                    "name": "English",
                    "iso6391Name": "en",
                    "score": 1
                }
            ]
        },
        {
            "id": "2",
            "detectedLanguages": [
                {
                    "name": "Spanish",
                    "iso6391Name": "es",
                    "score": 1
                }
            ]
        },
        {
            "id": "3",
            "detectedLanguages": [
                {
                    "name": "French",
                    "iso6391Name": "fr",
                    "score": 1
                }
            ]
        },
        {
            "id": "4",
            "detectedLanguages": [
                {
                    "name": "Chinese_Simplified",
                    "iso6391Name": "zh_chs",
                    "score": 1
                }
            ]
        },
        {
            "id": "5",
            "detectedLanguages": [
                {
                    "name": "Russian",
                    "iso6391Name": "ru",
                    "score": 1
                }
            ]
        }
    ],
```

### <a name="ambiguous-content"></a>Nejednoznačný obsah

Pokud analyzátor nelze analyzovat vstup, vrátí `(Unknown)`. Příkladem je, pokud odešlete textový blok, který se skládá pouze z číslovky.

```
    {
      "id": "5",
      "detectedLanguages": [
        {
          "name": "(Unknown)",
          "iso6391Name": "(Unknown)",
          "score": "NaN"
        }
      ]
```
### <a name="mixed-language-content"></a>Obsah jazyků

Obsah jazyků v rámci stejného dokumentu vrátí jazyk s největší reprezentací v obsahu, ale s nižší kladné hodnocení. Odráží hodnocení okrajového sílu posouzení. V následujícím příkladu je vstup směsicí angličtiny, španělštiny a francouzštiny. Analyzátor spočítá znaky v jednotlivých segmentech, aby určil převládající jazyk.

**Input** (Vstup)

```
{
  "documents": [
    {
      "id": "1",
      "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
    }
  ]
}
```

**Výstup**

Výsledný výstup se skládá z převládající jazyk, se skóre nižší než 1.0, což znamená nižší úroveň spolehlivosti.

```
{
  "documents": [
    {
      "id": "1",
      "detectedLanguages": [
        {
          "name": "Spanish",
          "iso6391Name": "es",
          "score": 0.9375
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro rozpoznávání jazyka s využitím rozhraní Text Analytics v Azure Cognitive Services. Následující body byly vysvětlení a jsme vám ukázali:

+ [Rozpoznávání jazyka](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) je k dispozici pro řadu jazyků, proměnných typu variant, dialekty a některé jazyky oblasti nebo jazykové verze.
+ Dokumenty JSON v textu žádosti zahrnovat ID a text.
+ Je požadavek POST `/languages` koncový bod s použitím přizpůsobeného [přístup ke key a koncového bodu](text-analytics-how-to-access-key.md) , který je platný pro vaše předplatné.
+ Výstup odezvy se skládá z identifikátory jazyka pro každé ID dokumentu. Výstup můžete Streamovat do jakékoli aplikaci, která přijímá JSON. Příklad aplikace patří aplikace Excel a Power BI, pár.

## <a name="see-also"></a>Viz také: 

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)</br>
 [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza mínění](text-analytics-how-to-sentiment-analysis.md)
