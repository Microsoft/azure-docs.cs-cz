---
title: Zjistit jazyk pomocí Analýza textu REST API
titleSuffix: Azure Cognitive Services
description: Rozpoznávání jazyka pomocí Analýza textu REST API z Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: d34f3a03e1bcd35c270d13c4dda57d0394a36e4b
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387790"
---
# <a name="example-detect-language-with-text-analytics"></a>Příklad: Zjistit jazyk pomocí Analýza textu

Funkce [rozpoznávání jazyka](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) služby Azure analýza textu REST API vyhodnocuje textové zadání pro každý dokument a vrátí identifikátory jazyka se skóre, které indikuje sílu analýzy.

Tato schopnost je užitečná pro úložiště obsahu, která shromažďují libovolné texty, u nichž není jazyk znám. Výsledky této analýzy můžete parsovat a určit, v kterém jazyce je vstupní dokument napsaný. Odpověď také vrátí skóre, které odráží spolehlivost modelu. Hodnota skóre je mezi 0 a 1.

Funkce Rozpoznávání jazyka dokáže detekovat široké spektrum jazyků, variant, dialektů a některých oblastí a regionů. Přesný seznam jazyků pro tuto funkci není publikovaný.

Pokud máte obsah vyjádřený v méně často používaném jazyce, můžete vyzkoušet funkci Rozpoznávání jazyka, abyste viděli, jestli vrátí kód. Odpověď pro jazyky, které se nedají detekovat `unknown`, je.

> [!TIP]
> Analýza textu také poskytuje image kontejneru Docker pro systém Linux pro detekci jazyka, takže můžete [nainstalovat a spustit kontejner analýza textu](text-analytics-how-to-install-containers.md) zavřít do vašich dat.

## <a name="preparation"></a>Příprava

Je nutné mít dokumenty JSON v tomto formátu: ID a text

Velikost dokumentu musí být v rozmezí 5 120 znaků na dokumentu. Pro každou kolekci můžete mít až 1 000 položek (ID). Kolekce se posílá v textu žádosti. Následující ukázka je příkladem obsahu, který můžete odeslat pro detekci jazyka:

```json
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

## <a name="step-1-structure-the-request"></a>Krok 1: Strukturování žádosti

Další informace o definici žádosti naleznete v tématu [Call the rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvoření žádosti POST. Dokumentaci k rozhraní API pro tento požadavek najdete v tématu [rozhraní API pro rozpoznávání jazyka](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

+ Nastavte koncový bod HTTP pro rozpoznávání jazyka. Použijte buď prostředek Analýza textu v Azure, nebo [kontejner analýza textu](text-analytics-how-to-install-containers.md)s instancemi. Do adresy URL `/text/analytics/v2.1/languages` musíte zahrnout. Například: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/languages`.

+ Nastavte hlavičku požadavku tak, aby obsahovala [přístupový klíč](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) pro operace analýza textu.

+ V textu požadavku zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použijte aplikaci [Postman](text-analytics-how-to-call-api.md) nebo otevřete **konzolu pro testování rozhraní API** v [dokumentaci](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) a vytvořte strukturu žádosti a pomocí příkazu POST ji odešlete do služby.

## <a name="step-2-post-the-request"></a>Krok 2: PUBLIKOVÁNÍ žádosti

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavků, které můžete poslat za minutu a sekundu, najdete v části [omezení dat](../overview.md#data-limits) v přehledu.

Nezapomeňte, že služba je bezstavová. Ve vašem účtu se neukládají žádná data. Výsledky se vrátí okamžitě v odpovědi.


## <a name="step-3-view-the-results"></a>Krok 3: Zobrazení výsledků

Všechny žádosti POST vrátí odpověď ve formátu JSON s ID a zjištěnými vlastnostmi.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo uložit výstup do souboru v místním systému. Pak importujte výstup do aplikace, kterou můžete použít k řazení, vyhledávání a manipulaci s daty.

Výsledky ukázkové žádosti by měly vypadat jako následující JSON. Všimněte si, že se jedná o jeden dokument s více položkami. Výstup je v angličtině. Identifikátory jazyka zahrnují popisný název a kód jazyka ve formátu [ISO 639-1](https://www.iso.org/standard/22109.html).

Kladné skóre 1.0 vyjadřuje nejvyšší možnou úroveň spolehlivosti analýzy.

```json
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
        "errors": []
    }
```

### <a name="ambiguous-content"></a>Nejednoznačný obsah

V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí `countryHint` parametru můžete zadat kód země se dvěma písmeny. Rozhraní API ve výchozím nastavení používá "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""` .

Například "nemožné" je běžné pro angličtinu i francouzštinu a pokud jsou uvedené s omezeným kontextem, bude odpověď vycházet z doporučení země "US". Je-li původ textu znám z Francie, který může být uveden jako pomocný parametr.

**Input** (Vstup)

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "impossible"
            },
            {
                "id": "2",
                "text": "impossible",
                "countryHint": "fr"
            }
        ]
    }
```

Služba teď má další kontext, aby se zajistilo lepší rozhodnutí: 

**Výstup**

```json
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
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

Pokud analyzátor nemůže analyzovat vstup, vrátí `(Unknown)`. Příkladem je, že odešlete textový blok, který se skládá pouze z arabských číslic.

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "score": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>Obsah pro smíšený jazyk

Obsah smíšených jazyků v rámci stejného dokumentu vrátí jazyk s největším vyjádřením v obsahu, ale s nižším kladným hodnocením. Hodnocení odráží mezní intenzitu posouzení. V následujícím příkladu je vstup směsicí angličtiny, španělštiny a francouzštiny. Analyzátor spočítá znaky v jednotlivých segmentech, aby určil převládající jazyk.

**Input** (Vstup)

```json
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

Výsledný výstup se skládá z předpřevládajícího jazyka s skóre menším než 1,0, což znamená slabší úroveň spolehlivosti.

```json
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

V tomto článku jste zjistili koncepty a pracovní postup pro detekci jazyka pomocí Analýza textu ve službě Azure Cognitive Services. Následující body byly vysvětleny a ukázaly:

+ [Detekce jazyka](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) je dostupná pro nejrůznější jazyky, varianty, dialekty a některé regionální nebo kulturní jazyky.
+ Dokumenty JSON v textu požadavku obsahují ID a text.
+ Požadavek post je na `/languages` koncový bod pomocí přizpůsobeného [přístupového klíče a koncového bodu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , který je platný pro vaše předplatné.
+ Výstup odpovědi se skládá z identifikátorů jazyka pro každé ID dokumentu. Výstup se dá streamovat do jakékoli aplikace, která přijímá JSON. Mezi příklady aplikací patří Excel a Power BI, aby se pojmenoval několik.

## <a name="see-also"></a>Viz také:

 [Přehled Analýza textu](../overview.md) Nejčastější dotazy – Nejčastější [dotazy](../text-analytics-resource-faq.md)</br>
 [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Analýza mínění](text-analytics-how-to-sentiment-analysis.md)
