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
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: b5936d68ba98054eb6d98860c6edfaf12d6736d8
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710121"
---
# <a name="example-detect-language-with-text-analytics"></a>Příklad: zjištění jazyka pomocí Analýza textu

Funkce [rozpoznávání jazyka](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) služby Azure analýza textu REST API vyhodnocuje textové zadání pro každý dokument a vrátí identifikátory jazyka se skóre, které indikuje sílu analýzy.

Tato schopnost je užitečná pro úložiště obsahu, která shromažďují libovolné texty, u nichž není jazyk znám. Výsledky této analýzy můžete parsovat a určit, v kterém jazyce je vstupní dokument napsaný. Odpověď také vrátí skóre, které odráží spolehlivost modelu. Hodnota skóre je mezi 0 a 1.

Funkce Rozpoznávání jazyka dokáže detekovat široké spektrum jazyků, variant, dialektů a některých oblastí a regionů. Přesný seznam jazyků pro tuto funkci není publikovaný.

Pokud máte obsah vyjádřený v méně často používaném jazyce, můžete vyzkoušet funkci Rozpoznávání jazyka, abyste viděli, jestli vrátí kód. Odpověď pro jazyky, které se nedají detekovat, je `unknown` .

> [!TIP]
> Analýza textu také poskytuje image kontejneru Docker pro systém Linux pro detekci jazyka, takže můžete [nainstalovat a spustit kontejner analýza textu](text-analytics-how-to-install-containers.md) zavřít do vašich dat.

## <a name="preparation"></a>Příprava

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

Je nutné mít dokumenty JSON v tomto formátu: ID a text.

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

## <a name="step-1-structure-the-request"></a>Krok 1: Struktura žádosti

Další informace o definici žádosti naleznete v tématu [Call the rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvořte žádost POST. Dokumentaci k rozhraní API pro tento požadavek najdete v tématu [rozhraní API pro rozpoznávání jazyka](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages).

+ Nastavte koncový bod HTTP pro rozpoznávání jazyka. Použijte buď prostředek Analýza textu v Azure, nebo [kontejner analýza textu](text-analytics-how-to-install-containers.md)s instancemi. `/text/analytics/v3.0/languages`Do adresy URL musíte zahrnout. Například: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`.

+ Nastavte hlavičku požadavku tak, aby obsahovala [přístupový klíč](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) pro operace analýza textu.

+ V textu požadavku zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použijte aplikaci [Postman](text-analytics-how-to-call-api.md) nebo otevřete **konzolu pro testování rozhraní API** v [dokumentaci](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) a vytvořte strukturu žádosti a pomocí příkazu POST ji odešlete do služby.

## <a name="step-2-post-the-request"></a>Krok 2: odeslání žádosti

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavků, které můžete poslat za minutu a sekundu, najdete v části [omezení dat](../overview.md#data-limits) v přehledu.

Nezapomeňte, že služba je bezstavová. Ve vašem účtu se neukládají žádná data. Výsledky se vrátí okamžitě v odpovědi.


## <a name="step-3-view-the-results"></a>Krok 3: zobrazení výsledků

Všechny žádosti POST vrátí odpověď ve formátu JSON s ID a zjištěnými vlastnostmi.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo uložit výstup do souboru v místním systému. Pak importujte výstup do aplikace, kterou můžete použít k řazení, vyhledávání a manipulaci s daty.

Výsledky ukázkové žádosti by měly vypadat jako následující JSON. Všimněte si, že se jedná o jeden dokument s více položkami. Výstup je v angličtině. Identifikátory jazyka zahrnují popisný název a kód jazyka ve formátu [ISO 639-1](https://www.iso.org/standard/22109.html).

Kladné skóre 1.0 vyjadřuje nejvyšší možnou úroveň spolehlivosti analýzy.

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "French",
                "iso6391Name": "fr",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "4",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "5",
            "detectedLanguage": {
                "name": "Russian",
                "iso6391Name": "ru",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

### <a name="ambiguous-content"></a>Nejednoznačný obsah

V některých případech může být obtížné nejednoznačnost jazyků na základě vstupu. Pomocí `countryHint` parametru můžete zadat kód země nebo oblasti ve dvou písmenech. Rozhraní API ve výchozím nastavení používá "US" jako výchozí countryHint. Chcete-li toto chování odebrat, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""` .

Například "nemožné" je běžné pro angličtinu i francouzštinu a v případě omezeného kontextu bude odezva založena na pomocném parametru země/oblasti USA. Pokud se ví, že text pochází z Francie, může být tato informace uvedena jako tip.

**Vstup**

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
                        "confidenceScore": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "confidenceScore": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

Pokud analyzátor nemůže analyzovat vstup, vrátí `(Unknown)` . Příkladem je, že odešlete textový blok, který se skládá pouze z arabských číslic.

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "confidenceScore": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>Obsah pro smíšený jazyk

Obsah smíšených jazyků v rámci stejného dokumentu vrátí jazyk s největším vyjádřením v obsahu, ale s nižším kladným hodnocením. Hodnocení odráží mezní intenzitu posouzení. V následujícím příkladu je vstup směsicí angličtiny, španělštiny a francouzštiny. Analyzátor spočítá znaky v jednotlivých segmentech, aby určil převládající jazyk.

**Vstup**

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
              "confidencescore": 0.94
            }
          ]
        }
      ],
      "errors": []
    }
```

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postup pro detekci jazyka pomocí Analýza textu ve službě Azure Cognitive Services. Následující body byly vysvětleny a ukázaly:

+ [Detekce jazyka](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) je dostupná pro nejrůznější jazyky, varianty, dialekty a některé regionální nebo kulturní jazyky.
+ Dokumenty JSON v textu požadavku obsahují ID a text.
+ Požadavek POST je na `/languages` koncový bod pomocí přizpůsobeného [přístupového klíče a koncového bodu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , který je platný pro vaše předplatné.
+ Výstup odpovědi se skládá z identifikátorů jazyka pro každé ID dokumentu. Výstup se dá streamovat do jakékoli aplikace, která přijímá JSON. Mezi příklady aplikací patří Excel a Power BI, aby se pojmenoval několik.

## <a name="see-also"></a>Viz také

* [Přehled analýzy textu](../overview.md)
* [Použití klientské knihovny Analýza textu](../quickstarts/text-analytics-sdk.md)
* [Co je nového](../whats-new.md)
