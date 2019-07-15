---
title: Analýza subjektivního hodnocení s využitím REST API pro analýzu textu z Azure Cognitive Services | Dokumentace Microsoftu
description: Zjistěte, jak rozpoznávání mínění s využitím REST API pro analýzu textu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: c3004dd3910dd5fdafc933efa213c9f097310e87
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68001713"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>Příklad: Rozpoznávání mínění pomocí analýzy textu

[Rozhraní API pro analýzu subjektivního hodnocení Azure](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) textové zadání vyhodnotí a vrátí skóre mínění pro každý dokument. Skóre v rozsahu od 0 (negativní) až 1 (pozitivní).

Tato možnost je užitečná pro rozpoznání pozitivního a negativního mínění v sociálních médiích, zákaznických recenzích a diskuzních fórech. Obsah poskytujete vy. Modelů a tréninkových dat. poskytovaných službou.

Rozhraní API pro analýzu mínění v současné době podporuje angličtina, němčina, španělština a francouzštinu. Další jazyky jsou ve verzi Preview. Další informace najdete v tématu [Podporované jazyky](../text-analytics-supported-languages.md).

> [!TIP]
> Rozhraní API pro analýzu textu Azure také poskytuje Dockeru založených na Linuxu image kontejneru pro analýzu mínění, což vám umožní [nainstalovat a spustit kontejner pro analýzu textu](text-analytics-how-to-install-containers.md) blízko datům.

## <a name="concepts"></a>Koncepty

Analýza textu vygeneruje pomocí algoritmu pro klasifikaci strojového učení skóre mínění v rozsahu 0 až 1. Skóre blížící se 1 značí pozitivní mínění, zatímco skóre blížící se 0 značí negativní mínění. Model je předem natrénovaný pomocí velkého počtu textů s přidruženími mínění. V současné době není možné poskytnout vlastní trénovací data. Model využívá kombinaci postupů při analýze textu. Mezi dostupné techniky patří zpracování textu, část řeči analýzy, umístění aplikace word a přidružení aplikace word. Další informace o tomto algoritmu najdete v [základních informacích o analýze textu](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

Analýza mínění se provádí pro celý dokument, ne jako extrakce mínění u konkrétní entity v textu. V praxi existuje určitá tendence pro vyhodnocování přesnost pro zlepšení, když dokumenty obsahují jednu nebo dvě věty spíše než velkou část textu. Během fáze hodnocení objektivity model určuje, jestli je dokument jako celek objektivní, nebo obsahuje subjektivní mínění. Dokument, který je ve většině případů cíle nepodporuje průběh na fázi zjišťování mínění, což vede k 0,50 skóre, které se žádné další zpracování. Pro dokumenty, které pokračují v kanálu vygeneruje další fázi skóre nad nebo pod 0,50. Skóre závisí na míru mínění v dokumentu.

## <a name="preparation"></a>Příprava

Analýza subjektivního hodnocení vytváří výsledek, kvalitnější při jí přiřadit menší bloky textu pro práci na. Je to tedy přesně naopak než u extrakce klíčových frází, která vrací lepší výsledky pro větší bloky textu. Zvažte podle toho možnost restrukturalizace vstupů, abyste z obou operací získali co nejlepší výsledky.

Dokumenty JSON musí mít v tomto formátu: ID, text a jazyk.

Velikost dokumentu musí být v jednom dokumentu v části 5 120 znaků. Můžete mít až 1 000 položek (ID) na kolekci. Kolekce se posílá v textu žádosti. Následující příklad je příkladem obsah, který může odeslat pro analýzu subjektivního hodnocení:

```
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

Další informace o definici požadavku, najdete v části [volání rozhraní Text Analytics API](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvořte požadavek POST. Projděte si dokumentaci k rozhraní API pro tuto žádost, najdete v článku [rozhraní API pro analýzu mínění](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9).

+ Nastavení koncového bodu HTTP pro analýzu mínění pomocí prostředků pro analýzu textu v Azure nebo vytvořenou instanci [kontejneru pro analýzu textu](text-analytics-how-to-install-containers.md). Musí zahrnovat `/sentiment` prostředků: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`.

+ Nastavte hlavičku žádosti tak, aby obsahovala přístupový klíč pro operace analýzy textu. Další informace najdete v tématu [najít koncových bodů a přístupové klíče](text-analytics-how-to-access-key.md).

+ V textu požadavku zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použití [Postman](text-analytics-how-to-call-api.md) nebo otevřít **testovací rozhraní API konzoly** v [dokumentaci](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) struktury požadavku a publikuje je do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslat žádost

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavky můžete odesílat za minutu a sekundu, najdete v článku [limity dat](../overview.md#data-limits) části v přehledu.

Nezapomeňte, že služba je bezstavová. Ve vašem účtu se neukládají žádná data. Výsledky se vrátí okamžitě v odpovědi.


## <a name="step-3-view-the-results"></a>Krok 3: Zobrazení výsledků

Analýza subjektivního hodnocení klasifikuje text jako převážně kladné nebo záporné. Přiřadí skóre v rozsahu od 0 do 1. Hodnoty blížící se 0,5 představují neutrální nebo neurčité mínění. Skóre 0,5 indikuje neutralitu. Pokud řetězec nelze analyzovat mínění, nebo nemá žádné mínění, skóre je vždy 0,5 přesně. Když například zadáte řetězec ve španělštině s kódem jazyka pro angličtinu, je skóre 0,5.

Výstup se vrátí okamžitě. Můžete Streamovat výsledky do aplikace, která přijímá JSON nebo uložte výstup do souboru v místním systému. Importujte výstup do aplikace, která slouží k řazení, hledání a manipulaci s daty.

Následující příklad ukazuje odpověď pro kolekce dokumentů, v tomto článku:

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

## <a name="sentiment-analysis-v3-public-preview"></a>Zabarvení analýzy v3 ve verzi public preview

[Příští verzi analýzu subjektivního hodnocení](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-preview/operations/56f30ceeeda5650db055a3c9) je teď dostupná ve veřejné verzi preview. Poskytuje významná vylepšení v přesnost a podrobné informace o kategorizaci text a vyhodnocování rozhraní API. 

> [!NOTE]
> * Formát požadavku v3 analýzu subjektivního hodnocení a [limity dat](../overview.md#data-limits) jsou stejné jako předchozí verze.
> * V tuto chvíli analýzu subjektivního hodnocení v3: 
>    * V současné době podporuje pouze angličtinu.  
>    * Je k dispozici v následujících oblastech: `Central US`, `Central Canada`, a `East Asia`.

|Funkce |Popis  |
|---------|---------|
|Pro zpřesnění     | Výrazné zlepšení při rozpoznávání pozitivního, neutrálního, negativního a smíšeného mínění v textových dokumentech oproti předchozím verzím.           |
|Dokumentu a na úrovni věty skóre mínění     | Rozpoznávání mínění v dokumentu i v jednotlivých větách. Pokud dokument obsahuje více vět, každé větě se také přiřadí skóre mínění.         |
|Kategorie mínění a skóre     | Rozhraní API nyní vrací kategorie mínění textu, kromě skóre mínění. Kategorie jsou `positive`, `negative`, `neutral`, a `mixed`.       |
| Vylepšení výstupu | Analýza subjektivního hodnocení nyní vrací informace pro celý textový dokument a jeho jednotlivé věty. |

### <a name="sentiment-labeling"></a>Označování mínění

V3 analýzy subjektivního hodnocení se můžete vrátit skóre a popisky na úrovni věty a dokumentu. Skóre a popisky jsou `positive`, `negative`, a `neutral`. Na úrovni dokumentu `mixed` mínění popisek (ne skóre) také může být vrácen. Mínění dokument je určen na základě agregace skóre vět.

| Věty mínění                                                        | Vrátí popisek dokumentu |
|---------------------------------------------------------------------------|----------------|
| Alespoň jednu pozitivní větu a zbytek věty je neutrální. | `positive`     |
| Alespoň jednu negativní větu a zbytek věty je neutrální.  | `negative`     |
| Alespoň jednu negativní větu a alespoň jednu pozitivní větu.         | `mixed`        |
| Všechny věty je neutrální.                                                 | `neutral`      |

### <a name="sentiment-analysis-v3-example-request"></a>Požadavek příklad v3 analýzy mínění

Následující kód JSON je příklad požadavku na novou verzi analýzu subjektivního hodnocení. Žádost o formátování je stejný jako předchozí verze:

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
  ]
}
```

### <a name="sentiment-analysis-v3-example-response"></a>Odpověď příklad v3 analýzy mínění

Formát požadavku je stejný jako předchozí verze, formát odpovědi se změnila. Následující kód JSON je příklad odpovědi z novou verzi rozhraní API:

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

Příklad můžete nalézt C# aplikaci, která volá tuto verzi analýzu subjektivního hodnocení na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro analýzu subjektivního hodnocení s využitím rozhraní Text Analytics v Azure Cognitive Services. Souhrn:

+ [Rozhraní API pro analýzu mínění](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) je k dispozici pro vybrané jazyky.
+ Dokumenty JSON v textu požadavku zahrnují ID, text a jazyka kódu.
+ Je požadavek POST `/sentiment` koncový bod s použitím přizpůsobeného [přístup ke key a koncového bodu](text-analytics-how-to-access-key.md) , který je platný pro vaše předplatné.
+ Výstup odezvy, který se skládá z skóre mínění pro každé ID dokumentu, můžete Streamovat do jakékoli aplikaci, která přijímá JSON. Příklad aplikace patří aplikace Excel a Power BI, pár.

## <a name="see-also"></a>Viz také: 

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)</br>
 [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Extrakce klíčových frází](text-analytics-how-to-keyword-extraction.md)
