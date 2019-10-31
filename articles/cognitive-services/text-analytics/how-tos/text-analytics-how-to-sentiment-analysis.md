---
title: Analýza mínění pomocí REST API Analýza textu z Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Naučte se detekovat mínění pomocí REST API Analýza textu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: d516b4f71f78a5da113356a4bdf6647585292999
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161676"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>Příklad: zjištění mínění pomocí Analýza textu

[Rozhraní API služby Azure analýza mínění](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) vyhodnocuje zadání textu a vrátí mínění skóre pro každý dokument. Skóre rozsahu od 0 (záporné) do 1 (kladné).

Tato možnost je užitečná pro rozpoznání pozitivního a negativního mínění v sociálních médiích, zákaznických recenzích a diskuzních fórech. Obsah je poskytován vámi. Služba poskytuje modely a školicí data.

V současné době rozhraní Analýza mínění API podporuje angličtinu, němčinu, španělštinu a francouzštinu. Další jazyky jsou ve verzi Preview. Další informace najdete v tématu [Podporované jazyky](../text-analytics-supported-languages.md).

> [!TIP]
> Azure rozhraní API pro analýzu textu taky poskytuje image kontejneru Docker na bázi Linux pro analýzu mínění, takže můžete [nainstalovat a spustit kontejner analýza textu](text-analytics-how-to-install-containers.md) blízko k datům.

## <a name="concepts"></a>Koncepty

Analýza textu vygeneruje pomocí algoritmu pro klasifikaci strojového učení skóre mínění v rozsahu 0 až 1. Skóre blížící se 1 značí pozitivní mínění, zatímco skóre blížící se 0 značí negativní mínění. Model je předem natrénovaný pomocí velkého počtu textů s přidruženími mínění. V současné době není možné zadat vlastní školicí data. Model používá kombinaci technik při analýze textu. Mezi techniky patří zpracování textu, analýza částí řeči, umístění slov a přidružení slov. Další informace o tomto algoritmu najdete v [základních informacích o analýze textu](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

Analýza mínění se provádí pro celý dokument, ne jako extrakce mínění u konkrétní entity v textu. V praxi je k dispozici lepší přesnost bodování při vyhodnocování, když dokumenty obsahují jednu nebo dvě věty místo velkého bloku textu. Během fáze hodnocení objektivity model určuje, jestli je dokument jako celek objektivní, nebo obsahuje subjektivní mínění. Dokument, který je většinou cílem, nepostupuje do fáze detekce mínění, která má za následek 0,50 skóre bez dalšího zpracování. Pro dokumenty, které pokračují v kanálu, vygeneruje další fáze skóre nad nebo pod 0,50. Skóre závisí na stupni mínění zjištěných v dokumentu.

## <a name="preparation"></a>Příprava

Analýza mínění přináší vyšší kvalitu, když dáte menšímu množství bloků textu, na kterém pracujete. Je to tedy přesně naopak než u extrakce klíčových frází, která vrací lepší výsledky pro větší bloky textu. Zvažte podle toho možnost restrukturalizace vstupů, abyste z obou operací získali co nejlepší výsledky.

Je nutné mít dokumenty JSON v tomto formátu: ID, text a jazyk.

Velikost dokumentu musí být v rozmezí 5 120 znaků na dokumentu. Pro každou kolekci můžete mít až 1 000 položek (ID). Kolekce se posílá v textu žádosti. Následující ukázka je příkladem obsahu, který můžete odeslat pro mínění analýzu:

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Krok 1: Struktura žádosti

Další informace o definici žádosti naleznete v tématu [Call the rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvoření žádosti POST. Dokumentaci k rozhraní API pro tento požadavek najdete v tématu [rozhraní API pro analýza mínění](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9).

+ Nastavte koncový bod HTTP pro analýzu mínění pomocí prostředku Analýza textu v Azure nebo vytvořeného [Analýza textu kontejneru](text-analytics-how-to-install-containers.md). Do adresy URL musíte zahrnout `/text/analytics/v2.1/sentiment`. Například: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`.

+ Nastavte hlavičku požadavku tak, aby obsahovala [přístupový klíč](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) pro operace analýza textu.

+ V textu požadavku zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použijte [post](text-analytics-how-to-call-api.md) nebo otevřete **konzolu testování API** v [dokumentaci](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) , abyste mohli strukturu žádosti strukturovat a odeslat ji do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslání žádosti

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavků, které můžete poslat za minutu a sekundu, najdete v části [omezení dat](../overview.md#data-limits) v přehledu.

Nezapomeňte, že služba je bezstavová. Ve vašem účtu se neukládají žádná data. Výsledky se vrátí okamžitě v odpovědi.


## <a name="step-3-view-the-results"></a>Krok 3: zobrazení výsledků

Analyzátor mínění klasifikuje text jako převládající nebo negativní. Přiřadí skóre v rozsahu od 0 do 1. Hodnoty blížící se 0,5 představují neutrální nebo neurčité mínění. Skóre 0,5 indikuje neutralitu. Pokud řetězec nelze analyzovat pro mínění nebo nemá žádné mínění, skóre je vždy 0,5 přesně. Když například zadáte řetězec ve španělštině s kódem jazyka pro angličtinu, je skóre 0,5.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo uložit výstup do souboru v místním systému. Pak importujte výstup do aplikace, kterou můžete použít k řazení, vyhledávání a manipulaci s daty.

Následující příklad ukazuje odpověď pro kolekci dokumentů v tomto článku:

```json
    {
        "documents": [
            {
                "score": 0.9999237060546875,
                "id": "1"
            },
            {
                "score": 0.0000540316104888916,
                "id": "2"
            },
            {
                "score": 0.99990355968475342,
                "id": "3"
            },
            {
                "score": 0.980544924736023,
                "id": "4"
            },
            {
                "score": 0.99996328353881836,
                "id": "5"
            }
        ],
        "errors": []
    }
```

## <a name="sentiment-analysis-v3-public-preview"></a>Verze Public Preview Analýza mínění V3

[Další verze analýza mínění](https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/56f30ceeeda5650db055a3c9) je nyní k dispozici ve verzi Public Preview. Přináší významné vylepšení přesnosti a podrobností o kategorizaci a bodování textu rozhraní API.

> [!NOTE]
> * Formát požadavku Analýza mínění v3 a [omezení dat](../overview.md#data-limits) jsou stejné jako předchozí verze.
> * V tuto chvíli Analýza mínění V3:
>    * Aktuálně podporuje angličtinu (`en`), japonštinu (`ja`), zjednodušenou čínštinu (`zh-Hans`), tradiční čínštinu (`zh-Hant`), francouzština (`fr`), italština (`it`), španělština (`es`), holandština (`nl`), portugalština (`pt`) a němčina (`de`) jazyky.
>    * Je k dispozici v následujících oblastech: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`a `West US 2`.

|Funkce |Popis  |
|---------|---------|
|Vylepšená přesnost     | Výrazné zlepšení při rozpoznávání pozitivního, neutrálního, negativního a smíšeného mínění v textových dokumentech oproti předchozím verzím.           |
|Mínění skóre dokumentu a na úrovni věty     | Rozpoznávání mínění v dokumentu i v jednotlivých větách. Pokud dokument obsahuje více vět, každé větě se také přiřadí skóre mínění.         |
|Popisky a bodování mínění     | Rozhraní API teď kromě míněního skóre vrátí mínění kategorie pro text. Kategorie jsou `positive`, `negative`, `neutral`a `mixed`.       |
| Vylepšený výstup | Analýza mínění nyní vrací informace pro celý textový dokument i jeho jednotlivé věty. |
| parametr verze modelu | Volitelný parametr pro výběr verze Analýza textuho modelu, který se používá pro vaše data. |

### <a name="sentiment-labeling"></a>Mínění popisky

Analýza mínění V3 může vracet skóre a popisky na úrovni věty a dokumentu. Skóre a popisky jsou `positive`, `negative`a `neutral`. Na úrovni dokumentu může být také vrácen popisek `mixed` mínění (nikoli skóre). Mínění dokumentu je určeno agregací skóre vět.

| Mínění věty                                                        | Popisek vráceného dokumentu |
|---------------------------------------------------------------------------|----------------|
| Nejméně jedna kladná věta a zbytek vět jsou neutrální. | `positive`     |
| Alespoň jedna záporná věta a zbytek vět jsou neutrální.  | `negative`     |
| Alespoň jedna záporná věta a alespoň jedna kladná věta.         | `mixed`        |
| Všechny věty jsou neutrální.                                                 | `neutral`      |

### <a name="model-versioning"></a>Správa verzí modelů

Počínaje verzí 3,0 vám rozhraní API pro analýzu textu umožňuje zvolit model Analýza textu použitý pro vaše data. Použijte parametr Optional `model-version` k výběru verze modelu v rámci vašich požadavků. Pokud tento parametr není zadán, rozhraní API bude ve výchozím nastavení `latest`, což je nejnovější stabilní verze modelu.

Dostupné verze modelu:
* `2019-10-01` (`latest`)

Každá odpověď z koncových bodů V3 obsahuje pole `model-version` určující verzi modelu, která byla použita.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```

### <a name="sentiment-analysis-v3-example-request"></a>Ukázkový požadavek Analýza mínění V3

Následující kód JSON je příkladem požadavku vytvořeného v nové verzi Analýza mínění. Formát požadavku je stejný jako předchozí verze:

```json
    {
        "documents": [
        {
            "language": "en",
            "id": "1",
            "text": "Hello world. This is some input text that I love."
        },
        {
            "language": "en",
            "id": "2",
            "text": "It's incredibly sunny outside! I'm so happy."
        }
        ],
    }
```

### <a name="sentiment-analysis-v3-example-response"></a>Příklad odpovědi Analýza mínění V3

I když je formát žádosti stejný jako předchozí verze, změnil se formát odpovědi. Následující JSON je příklad reakce z nové verze rozhraní API:

```json
    {
        "documents": [
            {
                "id": "1",
                "sentiment": "positive",
                "documentScores": {
                    "positive": 0.98570585250854492,
                    "neutral": 0.0001625834556762,
                    "negative": 0.0141316400840878
                },
                "sentences": [
                    {
                        "sentiment": "neutral",
                        "sentenceScores": {
                            "positive": 0.0785155147314072,
                            "neutral": 0.89702343940734863,
                            "negative": 0.0244610067456961
                        },
                        "offset": 0,
                        "length": 12
                    },
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.98570585250854492,
                            "neutral": 0.0001625834556762,
                            "negative": 0.0141316400840878
                        },
                        "offset": 13,
                        "length": 36
                    }
                ]
            },
            {
                "id": "2",
                "sentiment": "positive",
                "documentScores": {
                    "positive": 0.89198976755142212,
                    "neutral": 0.103382371366024,
                    "negative": 0.0046278294175863
                },
                "sentences": [
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.78401315212249756,
                            "neutral": 0.2067587077617645,
                            "negative": 0.0092281140387058
                        },
                        "offset": 0,
                        "length": 30
                    },
                    {
                        "sentiment": "positive",
                        "sentenceScores": {
                            "positive": 0.99996638298034668,
                            "neutral": 0.0000060341349126,
                            "negative": 0.0000275444017461
                        },
                        "offset": 31,
                        "length": 13
                    }
                ]
            }
        ],
        "errors": []
    }
```

### <a name="example-c-code"></a>Příklad C# kódu

Můžete najít ukázkovou C# aplikaci, která volá tuto verzi analýza mínění na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postup pro analýzu mínění pomocí Analýza textu ve službě Azure Cognitive Services. Souhrn:

+ [Rozhraní analýza mínění API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) je k dispozici pro vybrané jazyky.
+ Dokumenty JSON v textu požadavku zahrnují ID, text a kód jazyka.
+ Požadavek POST je na `/sentiment` koncový bod pomocí přizpůsobeného [přístupového klíče a koncového bodu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , který je platný pro vaše předplatné.
+ Výstup odpovědi, který se skládá z mínění skóre pro každé ID dokumentu, se může streamovat do libovolné aplikace, která přijímá JSON. Mezi příklady aplikací patří Excel a Power BI, aby se pojmenoval několik.

## <a name="see-also"></a>Další informace najdete v tématech

 [Analýza textu přehled](../overview.md) [častých otázek (FAQ)](../text-analytics-resource-faq.md)</br>
 [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Extrakce klíčových frází](text-analytics-how-to-keyword-extraction.md)
