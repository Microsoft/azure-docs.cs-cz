---
title: Zjišťování jazyce s využitím REST API pro analýzu textu | Dokumentace Microsoftu
description: Jak zjistit jazyk s využitím REST API pro analýzu textu z Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 549ef0d073a740edf700c54d02a0d9ef247db0b7
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245083"
---
# <a name="example-how-to-detect-language-with-text-analytics"></a>Příklad: Jak detekovat jazyce s využitím rozhraní Text Analytics

[Rozhraní API pro rozpoznávání jazyka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) vyhodnotí textový vstup a pro každý dokument vrátí identifikátory jazyka se skóre, které označuje sílu analýzy. Analýza textu rozpozná až 120 jazyků.

Tato schopnost je užitečná pro úložiště obsahu, která shromažďují libovolné texty, u nichž není jazyk znám. Výsledky této analýzy můžete parsovat a určit, v kterém jazyce je vstupní dokument napsaný. Spolu s odpovědí se vrátí také skóre, které odráží spolehlivost modelu (jedná se o hodnotu 0 až 1).

> [!TIP]
> Rozhraní text Analytics také poskytuje Dockeru založených na Linuxu image kontejneru pro rozpoznávání jazyka, tak, aby se [nainstalovat a spustit kontejner pro analýzu textu](text-analytics-how-to-install-containers.md) blízko datům.

## <a name="preparation"></a>Příprava

Musíte mít dokumenty JSON v tomto formátu: ID, text.

Dokument nesmí obsahovat více než 5 000 znaků a v každé kolekci můžete mít až 1 000 položek (ID). Kolekce se posílá v textu žádosti. Následuje příklad obsahu, který můžete odeslat pro rozpoznání jazyka.

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

Podrobnosti o definici žádosti najdete v článku o [volání rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvořte žádost **POST**. Projděte si dokumentaci k rozhraní API pro tuto žádost: [Rozhraní API pro detekci jazyka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)

+ Nastavení koncového bodu HTTP pro rozpoznávání jazyka pomocí prostředek pro analýzu textu v Azure nebo vytvořenou instanci [kontejneru pro analýzu textu](text-analytics-how-to-install-containers.md). Musí obsahovat prostředek `/languages`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`.

+ Nastavte hlavičku žádosti tak, aby obsahovala přístupový klíč pro operace analýzy textu. Další informace najdete v článku, který se věnuje [vyhledání koncových bodů a přístupových klíčů](text-analytics-how-to-access-key.md).

+ V textu žádosti zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použijte aplikaci [Postman](text-analytics-how-to-call-api.md) nebo otevřete **konzolu pro testování rozhraní API** v [dokumentaci](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) a vytvořte strukturu žádosti a pomocí příkazu POST ji odešlete do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslat žádost

Analýza se provede po přijetí žádosti. Služba přijme maximálně 100 žádostí za minutu. Každá žádost může mít maximální velikost 1 MB.

Nezapomeňte, že služba je bezstavová. Ve vašem účtu se neukládají žádná data. Výsledky se vrátí okamžitě v odpovědi.


## <a name="step-3-view-results"></a>Krok 3: Zobrazení výsledků

Všechny žádosti POST vrací odpověď ve formátu JSON s ID a zjištěnými vlastnostmi.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo můžete výstup uložit do souboru v místním systému a potom ho naimportovat do aplikace, která umožňuje řadit a vyhledávat data a pracovat s nimi.

Výsledky ukázkové žádosti by měly vypadat jako následující JSON. Všimněte si, že se jedná o jeden dokument s více položkami. Výstup je v angličtině. Identifikátory jazyka zahrnují popisný název a kód jazyka ve formátu [ISO 639-1](https://www.iso.org/standard/22109.html).

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

Pokud analyzátor nemůže parsovat výstup (předpokládejme například, že jste odeslali blok textu, který je tvořen výhradně arabskými číslicemi), vrátí `(Unknown)`.

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
### <a name="mixed-language-content"></a>Obsah v různých jazycích

Obsah v různých jazycích v rámci stejného dokumentu vrátí jazyk s nejvyšší mírou zastoupení v obsahu, ale s nižším kladným hodnocením, které odráží nevýznamnou přesvědčivost hodnocení. V následujícím příkladu je vstup směsicí angličtiny, španělštiny a francouzštiny. Analyzátor spočítá znaky v jednotlivých segmentech, aby určil převládající jazyk.

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

Výsledný výstup tvoří převládající jazyk a skóre nižší než 1.0, které označuje nižší úroveň spolehlivosti.

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

V tomto článku jste se seznámili s koncepty a pracovním postupem rozpoznávání jazyka pomocí funkce Analýza textu ve službě Cognitive Services. Následující odkazy slouží jako rychlé připomenutí hlavních bodů, které jsme dříve vysvětlili a demonstrovali:

+ [Rozhraní API pro rozpoznávání jazyka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) je dostupné pro 120 jazyků.
+ Dokumenty JSON obsahují v textu žádosti ID a text.
+ Žádost POST je určená pro koncový bod `/languages` a používá individuální [přístupový klíč a koncový bod](text-analytics-how-to-access-key.md), které jsou platné pro dané předplatné.
+ Výstup odpovědi, který je tvořen identifikátory jazyka pro jednotlivá ID dokumentu, lze streamovat do libovolné aplikace, která podporuje JSON, včetně například Excelu a Power BI.

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)</br>
 [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza mínění](text-analytics-how-to-sentiment-analysis.md)
