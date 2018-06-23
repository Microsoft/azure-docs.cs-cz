---
title: 'Postupy: jazyk detekce v textu Analytics REST API (kognitivní služeb pro Microsoft v Azure) | Microsoft Docs'
description: Jak zjistit jazyk pomocí Text Analytics REST API v kognitivní služby společnosti Microsoft na platformě Azure v tomto kurzu návod.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: f8e2d9a36533c298addcf42d3cb2061e9c2d1ac7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342647"
---
# <a name="how-to-detect-language-in-text-analytics"></a>K zjištění jazyk v Analýza textu

[Rozhraní API jazyka detekce](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) vyhodnotí text vstupní a pro každý dokument a vrátí identifikátory jazyka se skóre, která určuje, jaká síla analýzy. Analýza textu rozpozná až 120 jazyky.

Tato funkce se hodí pro obsah ukládá tento shromažďování libovolný text, který jazyk neznámé. Výsledky kontroly k určení, který jazyk je používán vstupní dokument můžete analyzovat. Odpověď se vrátí taky skóre, což odráží spolehlivosti modelu (hodnota mezi 0 a 1).

## <a name="preparation"></a>Příprava

Dokumenty JSON musí mít v tomto formátu: id, text

Velikost dokumentu musí být v části 5 000 znaků na jednu dokumentu, a může mít až 1 000 položek (ID) na kolekci. Kolekce je odeslán v textu požadavku. Následuje příklad obsahu, který může odeslat zjišťování jazyka.

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
                "text": "Этот документ находится на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Krok 1: Struktury požadavku

Podrobnosti o definice žádosti lze nalézt v [jak volat rozhraní API Analytics Text](text-analytics-how-to-call-api.md). Pro usnadnění práce se revidovat následující body:

+ Vytvoření **POST** požadavku. Přečtěte si dokumentaci k rozhraní API pro tuto žádost: [rozhraní API jazyka detekce](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)

+ Nastavte koncový bod HTTP zjišťování jazyka. Musí zahrnovat `/languages` prostředků: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`

+ Nastavte hlavičku požadavku zahrnout přístupový klíč pro operace Analýza textu. Další informace najdete v tématu [jak najít koncových bodů a přístupové klíče](text-analytics-how-to-access-key.md).

+ V těle žádosti zadejte kolekci dokumentů JSON, který jste připravili pro tuto analýzu

> [!Tip]
> Použití [Postman](text-analytics-how-to-call-api.md) nebo otevřít **rozhraní API testování konzoly** v [dokumentace](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) a struktury požadavek POST do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslat požadavek

Po přijetí žádosti o provedení analýzy. Služba přijímá až 100 požadavky za minutu. Každý požadavek může být maximálně 1 MB.

Odvolat, zda je služba bezstavové. Žádná data se ukládají ve vašem účtu. Výsledky jsou vráceny okamžitě v odpovědi.


## <a name="step-3-view-results"></a>Krok 3: Zobrazení výsledků

Všech požadavků POST vrátí JSON formátu odpovědi ID a zjistila vlastnosti.

Výstup se vrátí okamžitě. Stream výsledky do aplikace, která přijímá JSON nebo uložte si výstup do souboru na lokálním systému a následně ho naimportovat do aplikace, která umožňuje řazení, vyhledávání a manipulovat s daty.

Výsledky pro příklad požadavek by měl vypadat jako následujícím kódu JSON. Všimněte si, že se jedná o jeden dokument s více položek. Výstup je v angličtině. Identifikátory jazyka zahrnují popisný název a kód jazyka v [ISO 639-1](https://www.iso.org/standard/22109.html) formátu.

Kladné skóre 1.0 vyjadřoval nejvyšší úroveň spolehlivosti možné analýzy.



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

### <a name="ambiguous-content"></a>Nejednoznačný obsahu

Pokud analyzátor nelze analyzovat vstup (například, že jste odeslali bloku textu, který se skládá pouze z číslovky), vrátí `(Unknown)`.

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
### <a name="mixed-language-content"></a>Smíšený jazyk obsahu

Smíšený jazyk obsahu v rámci stejného dokumentu vrátí jazyk s největší reprezentace obsah, ale s nižší kladné hodnocení odrážející okrajového síla tohoto hodnocení. V následujícím příkladu je vstup blend angličtina, španělština a francouzštinu. Nástroje analyzer vrátí počet znaků v každém segmentu Určuje jazyk, převládá.

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

Výsledný výstup se skládá z převládá jazyk, se skóre menší než 1,0 označující nižší úroveň spolehlivosti.

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

V tomto článku jste se dozvěděli, koncepty a pracovní postup zjišťování jazyka pomocí Analýza textu v kognitivní služby. Tady jsou rychlé připomenutí z hlavních bodů dříve vysvětlené a ukázán:

+ [Zjišťování rozhraní API jazyka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) je k dispozici pro 120 jazyky.
+ Dokumenty JSON v textu požadavku patří id a text.
+ Je požadavek POST `/languages` koncový bod, pomocí přizpůsobené [přístup klíč a koncový bod](text-analytics-how-to-access-key.md) , je platný pro vaše předplatné.
+ Odpověď výstupu, který se skládá z jazyka identifikátory pro každý dokument ID, Streamovat k jakékoli aplikaci, která přijímá formát JSON, včetně aplikace Excel a Power BI a další.

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled analýzy textu](../overview.md)  
 [Nejčastější dotazy (FAQ)](../text-analytics-resource-faq.md)</br>
 [Stránka produktu Analýza textu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza postojích](text-analytics-how-to-sentiment-analysis.md)
