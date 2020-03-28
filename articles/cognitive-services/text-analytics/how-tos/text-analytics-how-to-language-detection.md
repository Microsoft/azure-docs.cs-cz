---
title: Zjišťování jazyka pomocí rozhraní REST API analýzy textu
titleSuffix: Azure Cognitive Services
description: Zjišťování jazyka pomocí rozhraní API REST analýzy textových analýz z Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: d34f3a03e1bcd35c270d13c4dda57d0394a36e4b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70387790"
---
# <a name="example-detect-language-with-text-analytics"></a>Příklad: Zjišťování jazyka pomocí analýzy textu

Funkce [rozpoznávání jazyků](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) rozhraní REST rozhraní Azure Text Analytics vyhodnocuje zadávání textu pro každý dokument a vrací identifikátory jazyka se skóre, které označuje sílu analýzy.

Tato schopnost je užitečná pro úložiště obsahu, která shromažďují libovolné texty, u nichž není jazyk znám. Výsledky této analýzy můžete parsovat a určit, v kterém jazyce je vstupní dokument napsaný. Odpověď také vrátí skóre, které odráží důvěru modelu. Hodnota skóre je mezi 0 a 1.

Funkce Rozpoznávání jazyků dokáže rozpoznat širokou škálu jazyků, variant, dialektů a některých regionálních nebo kulturních jazyků. Přesný seznam jazyků pro tuto funkci není publikován.

Pokud máte obsah vyjádřený v méně často používaném jazyce, můžete zkusit funkci Rozpoznávání jazyka a zjistit, zda vrátí kód. Odpověď pro jazyky, které nelze `unknown`zjistit, je .

> [!TIP]
> Text Analytics také poskytuje linuxovou image kontejneru Dockerpro detekci jazyka, takže můžete [nainstalovat a spustit kontejner Analýzy textu](text-analytics-how-to-install-containers.md) v blízkosti vašich dat.

## <a name="preparation"></a>Příprava

Musíte mít dokumenty JSON v tomto formátu: ID a text.

Velikost dokumentu musí být menší než 5 120 znaků na dokument. Můžete mít až 1 000 položek (ID) na kolekci. Kolekce se posílá v textu žádosti. Následující ukázka je příkladem obsahu, který můžete odeslat ke zjištění jazyka:

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

Další informace o definici požadavku naleznete [v tématu Volání rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvořte žádost POST. Informace o dokumentaci k rozhraní API pro tento požadavek naleznete v [rozhraní API pro zjišťování jazyka](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

+ Nastavte koncový bod HTTP pro rozpoznávání jazyka. Použijte buď prostředek Analýzy textu v Azure, nebo [kontejner analýzy textu](text-analytics-how-to-install-containers.md)s vytvořena instance . Musíte zahrnout `/text/analytics/v2.1/languages` do adresy URL. Například: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/languages`.

+ Nastavte hlavičku požadavku tak, aby zahrnovala [přístupový klíč](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) pro operace Analýzy textu.

+ V textu požadavku zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použijte aplikaci [Postman](text-analytics-how-to-call-api.md) nebo otevřete **konzolu pro testování rozhraní API** v [dokumentaci](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) a vytvořte strukturu žádosti a pomocí příkazu POST ji odešlete do služby.

## <a name="step-2-post-the-request"></a>Krok 2: POST požadavek

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavků, které můžete odeslat za minutu a sekundu, naleznete v části [Omezení dat](../overview.md#data-limits) v přehledu.

Nezapomeňte, že služba je bezstavová. Ve vašem účtu se neukládají žádná data. Výsledky se vrátí okamžitě v odpovědi.


## <a name="step-3-view-the-results"></a>Krok 3: Zobrazení výsledků

Všechny požadavky POST vrátí odpověď ve formátu JSON s ID a zjištěnými vlastnostmi.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo uložit výstup do souboru v místním systému. Potom importujte výstup do aplikace, kterou můžete použít k řazení, vyhledávání a manipulaci s daty.

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

V některých případech může být obtížné rozptýlit jazyky na základě vstupu. `countryHint` Parametr můžete použít k určení dvoupísmenný kód země. Ve výchozím nastavení rozhraní API používá "US" jako výchozí zeměHint, chcete-li odebrat toto chování, můžete tento parametr obnovit nastavením této hodnoty na prázdný řetězec `countryHint = ""` .

Například "Nemožné" je společné pro angličtinu a francouzštinu, a pokud je uveden s omezeným kontextem odpověď bude založena na "USA" země tip. Pokud se ví, že text pochází z Francie, může být tato informace uvedena jako tip.

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

Služba má nyní další kontext, aby se lépe úsudek: 

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

Pokud analyzátor nemůže analyzovat vstup, vrátí `(Unknown)`. Příkladem je, pokud odešlete textový blok, který se skládá výhradně z arabských číslic.

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

### <a name="mixed-language-content"></a>Obsah ve smíšeném jazyce

Obsah ve smíšeném jazyce v rámci stejného dokumentu vrátí jazyk s největší reprezentací v obsahu, ale s nižším kladným hodnocením. Hodnocení odráží mezní sílu hodnocení. V následujícím příkladu je vstup směsicí angličtiny, španělštiny a francouzštiny. Analyzátor spočítá znaky v jednotlivých segmentech, aby určil převládající jazyk.

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

Výsledný výstup se skládá z převládajícího jazyka se skóre menším než 1,0, což naznačuje slabší úroveň spolehlivosti.

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

V tomto článku jste se naučili koncepty a pracovní postupy pro detekci jazyka pomocí analýzy textu ve službě Azure Cognitive Services. Byly vysvětleny a prokázány následující body:

+ [Rozpoznávání jazyků](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) je k dispozici pro širokou škálu jazyků, variant, dialektů a některých regionálních nebo kulturních jazyků.
+ Dokumenty JSON v textu požadavku obsahují ID a text.
+ Požadavek POST je `/languages` ke koncovému bodu pomocí přizpůsobeného [přístupového klíče a koncového bodu,](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) který je platný pro vaše předplatné.
+ Výstup odpovědi se skládá z identifikátorů jazyka pro každé ID dokumentu. Výstup může být datový proud do libovolné aplikace, která přijímá JSON. Příklady aplikací zahrnují Excel a Power BI, abychom jmenovali jen některé.

## <a name="see-also"></a>Viz také

 [Přehled analýzy textu](../overview.md) [Nejčastější dotazy (NEJČASTĚJŠÍ DOTAZY)](../text-analytics-resource-faq.md)</br>
 [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza mínění](text-analytics-how-to-sentiment-analysis.md)
