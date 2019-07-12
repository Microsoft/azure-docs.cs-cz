---
title: Analýza mínění s využitím rozhraní Text Analytics z Azure Cognitive Services | Dokumentace Microsoftu
description: Přečtěte si, jak zjistit mínění pomocí rozhraní REST API pro analýzu textu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: e17b68dfd63952d0c8c81415b090b047c5808e2e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797793"
---
# <a name="example-how-to-detect-sentiment-with-text-analytics"></a>Příklad: Rozpoznávání mínění s využitím Analýzy textu

[Rozhraní API pro analýzu mínění](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) vyhodnocuje textový vstup a vrací skóre mínění pro každý dokument v rozsahu 0 (negativní) až 1 (pozitivní).

Tato možnost je užitečná pro rozpoznání pozitivního a negativního mínění v sociálních médiích, zákaznických recenzích a diskuzních fórech. Obsah pochází od vás, modely a trénovací data poskytuje služba.

Pro analýzu mínění se momentálně podporuje angličtina, němčina, španělština a francouzština. Další jazyky jsou ve verzi Preview. Další informace najdete v tématu [Podporované jazyky](../text-analytics-supported-languages.md).

> [!TIP]
> Rozhraní text Analytics také poskytuje Dockeru založených na Linuxu image kontejneru pro analýzu mínění, což vám umožní [nainstalovat a spustit kontejner pro analýzu textu](text-analytics-how-to-install-containers.md) blízko datům.

## <a name="concepts"></a>Koncepty

Analýza textu vygeneruje pomocí algoritmu pro klasifikaci strojového učení skóre mínění v rozsahu 0 až 1. Skóre blížící se 1 značí pozitivní mínění, zatímco skóre blížící se 0 značí negativní mínění. Model je předem natrénovaný pomocí velkého počtu textů s přidruženími mínění. V současné době není možné dodat vlastní trénovací data. Při analýze textu model používá různé postupy, například zpracování textu, analýzu částí řeči, rozmístění slov a asociace slov. Další informace o tomto algoritmu najdete v [základních informacích o analýze textu](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

Analýza mínění se provádí pro celý dokument, ne jako extrakce mínění u konkrétní entity v textu. Dá se říct, že v praxi bývá skóre přesnější, když dokumenty obsahují jednu nebo dvě věty, a ne rozsáhlý blok textu. Během fáze hodnocení objektivity model určuje, jestli je dokument jako celek objektivní, nebo obsahuje subjektivní mínění. Dokument, který je převážně objektivní, nepřechází do fáze rozpoznávání mínění, dále se nezpracovává a jeho výsledné skóre bude 0,5. Pro dokumenty pokračující do další fáze se vygeneruje skóre vyšší nebo nižší než 0,5 v závislosti na míře subjektivního mínění rozpoznané v daném dokumentu.

## <a name="preparation"></a>Příprava

Výsledky analýzy mínění jsou kvalitnější pro menší bloky textu. Je to tedy přesně naopak než u extrakce klíčových frází, která vrací lepší výsledky pro větší bloky textu. Zvažte podle toho možnost restrukturalizace vstupů, abyste z obou operací získali co nejlepší výsledky.

Dokumenty JSON musí mít v tomto formátu: ID, text, jazyk

Velikost dokumentu musí být v jednom dokumentu v části 5 120 znaků a může mít až 1 000 položek (ID) na kolekci. Kolekce se posílá v textu žádosti. Následuje příklad obsahu, který můžete odeslat pro analýzu mínění.

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

Podrobnosti o definici žádosti najdete v článku o [volání rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvořte žádost **POST**. Projděte si dokumentaci k rozhraní API pro tuto žádost: [Rozhraní API pro analýzu mínění](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

+ Nastavení koncového bodu HTTP pro analýzu mínění, pomocí prostředek pro analýzu textu v Azure nebo instance [kontejneru pro analýzu textu](text-analytics-how-to-install-containers.md). Musí obsahovat prostředek `/sentiment`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`.

+ Nastavte hlavičku žádosti tak, aby obsahovala přístupový klíč pro operace analýzy textu. Další informace najdete v článku, který se věnuje [vyhledání koncových bodů a přístupových klíčů](text-analytics-how-to-access-key.md).

+ V textu požadavku zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použijte aplikaci [Postman](text-analytics-how-to-call-api.md) nebo otevřete **konzolu pro testování rozhraní API** v [dokumentaci](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) a vytvořte strukturu žádosti a pomocí příkazu POST ji odešlete do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslat žádost

Analýza se provede po přijetí žádosti. Najdete v článku [limity dat](../overview.md#data-limits) části v přehledu o velikosti a počtu požadavky můžete odesílat za minutu a sekundu.

Nezapomeňte, že služba je bezstavová. Ve vašem účtu se neukládají žádná data. Výsledky se vrátí okamžitě v odpovědi.


## <a name="step-3-view-results"></a>Krok 3: Zobrazení výsledků

Analýza mínění klasifikuje text jako převážně pozitivní nebo negativní a přiřadí mu skóre v rozsahu 0 až 1. Hodnoty blížící se 0,5 představují neutrální nebo neurčité mínění. Skóre 0,5 indikuje neutralitu. Pokud u řetězce nelze analyzovat mínění nebo v něm žádné mínění není, bude skóre vždycky přesně 0,5. Když například zadáte řetězec ve španělštině s kódem jazyka pro angličtinu, je skóre 0,5.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo můžete výstup uložit do souboru v místním systému a potom ho naimportovat do aplikace, která umožňuje řadit a vyhledávat data a pracovat s nimi.

Následující příklad ukazuje odpověď pro kolekci dokumentů v tomto článku.

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

## <a name="sentiment-analysis-v3-public-preview"></a>Veřejná verze preview V3 analýzy mínění

[Příští verzi analýzu subjektivního hodnocení](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-preview/operations/56f30ceeeda5650db055a3c9) je teď dostupná ve veřejné verzi Preview, poskytuje významná vylepšení v přesnost a podrobné informace o kategorizaci textu rozhraní API a vyhodnocování. 

> [!NOTE]
> * Formát požadavku v3 analýzy subjektivního hodnocení a [limity dat](../overview.md#data-limits) jsou stejné jako předchozí verze.
> * V tuto chvíli analýzu subjektivního hodnocení V3: 
>    * Aktuálně podporuje pouze angličtinu.  
>    * Je k dispozici v následujících oblastech: `Central US`, `Central Canada`, `East Asia` 

|Funkce |Popis  |
|---------|---------|
|Pro zpřesnění     | Výrazné zlepšení zjišťování kladné, neutrální, záporná a smíšené mínění v textu dokumenty porovnání s předchozími verzemi.           |
|Dokument a skóre mínění na úrovni vět     | Rozpoznávání mínění dokument a jeho jednotlivé věty. Pokud dokument obsahuje více věty, jednotlivé věty je také přiřazený skóre mínění.         |
|Kategorie mínění a skóre     | Rozhraní API nyní vrací kategorie mínění (`positive`, `negative`, `neutral` a `mixed`) pro text, kromě skóre mínění.        |
| Vylepšení výstupu | Analýza subjektivního hodnocení nyní vrací informace pro celý textový dokument a jeho jednotlivé věty. |

### <a name="sentiment-labeling"></a>Označování mínění

Analýza subjektivního hodnocení V3 se můžete vrátit skóre a popisky (`positive`, `negative`, a `neutral`) na úrovni věty a dokumentu. Na úrovni dokumentu `mixed` mínění popisek (ne skóre) lze také vrátit. Mínění dokumentu je určen na základě agregace skóre jeho věty.

| Věty mínění                                                        | Vrátí popisek dokumentu |
|---------------------------------------------------------------------------|----------------|
| Alespoň jednu pozitivní větu a zbytek věty je neutrální. | `positive`     |
| Alespoň jednu negativní větu a zbytek věty je neutrální.  | `negative`     |
| Alespoň jednu negativní větu a alespoň jednu pozitivní větu.         | `mixed`        |
| Všechny věty je neutrální.                                                 | `neutral`      |

### <a name="sentiment-analysis-v3-example-request"></a>Příklad žádosti V3 analýzy mínění

Následující kód JSON je příklad požadavku na novou verzi analýzu subjektivního hodnocení. Všimněte si, že žádost o formátování je stejný jako předchozí verze:

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

### <a name="sentiment-analysis-v3-example-response"></a>Analýza subjektivního hodnocení V3 příklad odpovědi

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

V tomto článku jste se seznámili s koncepty a pracovním postupem analýzy mínění pomocí funkce Analýza textu ve službě Cognitive Services. Souhrn:

+ [Rozhraní API pro analýzu mínění](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) je k dispozici pro vybrané jazyky.
+ Dokumenty JSON v textu požadavku zahrnují ID, text a jazyka kódu.
+ Žádost POST je určená pro koncový bod `/sentiment` a používá individuální [přístupový klíč a koncový bod](text-analytics-how-to-access-key.md), které jsou platné pro dané předplatné.
+ Výstup odpovědi, který tvoří skóre mínění pro jednotlivá ID dokumentu, lze streamovat do libovolné aplikace, která přijímá JSON, včetně například Excelu a Power BI.

## <a name="see-also"></a>Viz také: 

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)</br>
 [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Extrakce klíčových frází](text-analytics-how-to-keyword-extraction.md)
