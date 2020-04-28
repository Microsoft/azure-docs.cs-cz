---
title: Provedení analýzy mínění s využitím Analýza textu REST API
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak zjistit mínění v textu pomocí Analýza textu REST API služby Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 04/27/2020
ms.author: aahi
ms.openlocfilehash: 99a62daf6dced88efd9bda591a0ca44a8b259a75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195634"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Postupy: zjištění mínění pomocí rozhraní API pro analýzu textu

Funkce Analýza mínění rozhraní API pro analýzu textu vyhodnotí text a vrátí mínění skóre a štítky pro každou větu. To je užitečné při zjišťování pozitivních a záporných mínění v sociálních médiích, na revizích zákazníků, diskuzních fórech a dalších. Modely AI používané rozhraním API jsou součástí služby, stačí ale odeslat obsah k analýze.

> [!TIP]
> Analýza textu také poskytuje image kontejneru Docker pro systém Linux pro detekci jazyka, takže můžete [nainstalovat a spustit kontejner analýza textu](text-analytics-how-to-install-containers.md) zavřít do vašich dat.

Analýza mínění podporuje široké spektrum jazyků, ve verzi Preview. Další informace najdete v tématu [Podporované jazyky](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Koncepty

Rozhraní API pro analýzu textu používá algoritmus klasifikace Machine Learning k vygenerování mínění skóre mezi 0 a 1. Skóre blížící se 1 značí pozitivní mínění, zatímco skóre blížící se 0 značí negativní mínění. Analýza mínění se provádí v celém dokumentu místo individuálních entit v textu. To znamená, že mínění skóre se vrátí na úrovni dokumentu nebo věty. 

Použitý model je předem vyškolený s rozsáhlými corpusy textových a mínění přidružení. Využívá kombinaci technik pro analýzu, včetně zpracování textu, analýzy částí řeči, umístění slov a přidružení slov. Další informace o tomto algoritmu najdete v [základních informacích o analýze textu](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/). V současné době není možné zadat vlastní školicí data. 

Je k dispozici pro lepší přesnost bodování, když dokumenty obsahují méně vět, nikoli velký blok textu. Během fáze hodnocení objektivity model určuje, jestli je dokument jako celek objektivní, nebo obsahuje subjektivní mínění. Dokument, který je většinou cílem, nepostupuje do fáze detekce mínění, která má za následek 0,50 skóre bez dalšího zpracování. Pro dokumenty, které pokračují v kanálu, vygeneruje další fáze skóre nad nebo pod 0,50. Skóre závisí na stupni mínění zjištěných v dokumentu.

## <a name="sentiment-analysis-versions-and-features"></a>Analýza mínění verze a funkce

Rozhraní API pro analýzu textu nabízí dvě verze Analýza mínění-v2 a v3. Analýza mínění V3 (Public Preview) přináší významná vylepšení přesnosti a podrobností o kategorizaci a bodování textu rozhraní API.

> [!NOTE]
> * Formát požadavku Analýza mínění v3 a [omezení dat](../overview.md#data-limits) jsou stejné jako předchozí verze.
> * Analýza mínění v3 je k dispozici v následujících oblastech `Australia East`: `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`, a `West US 2`.

| Funkce                                   | Analýza mínění v2 | Analýza mínění V3 |
|-------------------------------------------|-----------------------|-----------------------|
| Metody pro jednotlivé a dávkové požadavky    | ×                     | ×                     |
| Mínění skóre celého dokumentu  | ×                     | ×                     |
| Mínění skóre pro jednotlivé věty |                       | ×                     |
| Mínění popisky                        |                       | ×                     |
| Správa verzí modelů                   |                       | ×                     |

#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

### <a name="sentiment-scoring"></a>Bodování mínění

Analýza mínění V3 klasifikuje text pomocí popisků mínění (popsaných níže). Vracená skóre reprezentují jistotu modelu, že text je buď kladný, záporný nebo neutrální. Vyšší hodnoty označují větší jistotu. 

### <a name="sentiment-labeling"></a>Mínění popisky

Analýza mínění V3 vrátí mínění popisky na větu a na úrovni dokumentu (`positive`, `negative`a `neutral`) spolu s hodnocením spolehlivosti. Popisek `mixed` mínění může být také vrácen na úrovni dokumentu. 

Mínění dokumentu je určena níže:

| Mínění věty                                                                            | Popisek vráceného dokumentu |
|-----------------------------------------------------------------------------------------------|-------------------------|
| V dokumentu je `positive` alespoň jedna věta. Zbytek vět je `neutral`. | `positive`              |
| V dokumentu je `negative` alespoň jedna věta. Zbytek vět je `neutral`. | `negative`              |
| V dokumentu je `negative` alespoň jedna věta a alespoň `positive` jedna věta.    | `mixed`                 |
| Všechny věty v dokumentu jsou `neutral`.                                                  | `neutral`               |

### <a name="model-versioning"></a>Správa verzí modelů

> [!NOTE]
> Verze modelu pro analýzu mínění je dostupná od verze `v3.0-preview.1`.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>Příklad kódu jazyka C#

Můžete najít ukázkovou aplikaci v jazyce C#, která volá tuto verzi Analýza mínění na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/TextAnalyticsSentiment.cs).


#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

### <a name="sentiment-scoring"></a>Bodování mínění

Analyzátor mínění klasifikuje text jako převládající nebo negativní. Přiřadí skóre v rozsahu od 0 do 1. Hodnoty blížící se 0,5 představují neutrální nebo neurčité mínění. Skóre 0,5 indikuje neutralitu. Pokud řetězec nelze analyzovat pro mínění nebo nemá žádné mínění, skóre je vždy 0,5 přesně. Když například zadáte řetězec ve španělštině s kódem jazyka pro angličtinu, je skóre 0,5.

---

## <a name="sending-a-rest-api-request"></a>Odesílá se žádost o REST API. 

### <a name="preparation"></a>Příprava

Analýza mínění přináší vyšší kvalitu, když dáte menší množství textu, na kterém bude pracovat. Je to tedy přesně naopak než u extrakce klíčových frází, která vrací lepší výsledky pro větší bloky textu. Zvažte podle toho možnost restrukturalizace vstupů, abyste z obou operací získali co nejlepší výsledky.

Je nutné mít dokumenty JSON v tomto formátu: ID, text a jazyk.

Velikost dokumentu musí být v rozmezí 5 120 znaků na dokumentu. Pro každou kolekci můžete mít až 1 000 položek (ID). Kolekce se posílá v textu žádosti.

## <a name="structure-the-request"></a>Strukturování žádosti

Vytvořte žádost POST. V následujících referenčních odkazech můžete [použít možnost post](text-analytics-how-to-call-api.md) nebo **Konzola pro testování API** k rychlému uspořádání a odeslání jednoho. 

#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

[Reference Analýza mínění V3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

[Reference k Analýza mínění v2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

Nastavte koncový bod HTTPS pro analýzu mínění pomocí prostředku Analýza textu v Azure nebo vytvořeného [Analýza textu kontejneru](text-analytics-how-to-install-containers.md). Musíte zahrnout správnou adresu URL pro verzi, kterou chcete použít. Příklad:

> [!NOTE]
> Klíč a koncový bod pro váš Analýza textu prostředek najdete na webu Azure Portal. Budou se nacházet na stránce **rychlý Start** prostředku v části **Správa prostředků**. 

#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

Nastavte hlavičku požadavku tak, aby obsahovala klíč rozhraní API pro analýzu textu. V textu požadavku zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

### <a name="example-sentiment-analysis-request"></a>Příklad Analýza mínění žádosti 

Následuje příklad obsahu, který můžete odeslat pro analýzu mínění. Formát požadavku je pro obě verze rozhraní API stejný.
    
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

### <a name="post-the-request"></a>Publikování žádosti

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavků, které můžete poslat za minutu a sekundu, najdete v části [omezení dat](../overview.md#data-limits) v přehledu.

Rozhraní API pro analýzu textu je Bezstavová. Ve vašem účtu se neukládají žádná data a výsledky se vrátí hned v odpovědi.


### <a name="view-the-results"></a>Zobrazení výsledků

Analyzátor mínění klasifikuje text jako převládající nebo negativní. Přiřadí skóre v rozsahu od 0 do 1. Hodnoty blížící se 0,5 představují neutrální nebo neurčité mínění. Skóre 0,5 indikuje neutralitu. Pokud řetězec nelze analyzovat pro mínění nebo nemá žádné mínění, skóre je vždy 0,5 přesně. Když například zadáte řetězec ve španělštině s kódem jazyka pro angličtinu, je skóre 0,5.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo uložit výstup do souboru v místním systému. Pak importujte výstup do aplikace, kterou můžete použít k řazení, vyhledávání a manipulaci s daty. Vzhledem k podpoře vícejazyčných a Emoji může odpověď obsahovat posunutí textu. Další informace najdete v tématu [postup zpracování posunů](../concepts/text-offsets.md) .

#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>Příklad odpovědi Analýza mínění V3

Odpovědi z Analýza mínění V3 obsahují mínění popisky a skóre pro každou analyzovanou větu a dokument. `documentScores`se nevrátí, pokud je `mixed`mínění jmenovka dokumentu.

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

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>Příklad odpovědi Analýza mínění v2

Odpovědi z Analýza mínění v2 obsahují skóre mínění pro každý odeslaný dokument.

```json
{
  "documents": [{
    "id": "1",
    "score": 0.98690706491470337
  }, {
    "id": "2",
    "score": 0.95202046632766724
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postup pro analýzu mínění pomocí rozhraní API pro analýzu textu. Souhrn:

+ Analýza mínění je k dispozici pro vybrané jazyky ve dvou verzích.
+ Dokumenty JSON v textu požadavku zahrnují ID, text a kód jazyka.
+ Požadavek POST je na `/sentiment` koncový bod pomocí přizpůsobeného [přístupového klíče a koncového bodu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , který je platný pro vaše předplatné.
+ Výstup odpovědi, který se skládá z mínění skóre pro každé ID dokumentu, se může streamovat do libovolné aplikace, která přijímá JSON. Například Excel a Power BI.

## <a name="see-also"></a>Viz také

* [Přehled Analýza textu](../overview.md)
* [Použití klientské knihovny Analýza textu](../quickstarts/text-analytics-sdk.md)
* [Co je nového](../whats-new.md)
