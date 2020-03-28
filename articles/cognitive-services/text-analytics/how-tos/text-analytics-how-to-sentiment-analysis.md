---
title: Provádění analýzy mínění pomocí rozhraní REST API analýzy textu
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak zjistit mínění v textu pomocí rozhraní REST API Azure Cognitive Services Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 03/09/2020
ms.author: aahi
ms.openlocfilehash: b3c112876bfd2578e6ebaa95c6902aa9b8f832d9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "79203453"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Postup: Zjišťování mínění pomocí rozhraní API pro analýzu textu

Funkce analýzy mínění rozhraní Text Analytics API vyhodnocuje text a vrací skóre mínění a popisky pro každou větu. To je užitečné pro detekci pozitivních a negativních nálad v sociálních médiích, recenzích zákazníků, diskusních fórech a dalších. Modely AI používané rozhraním API jsou poskytovány službou, stačí odeslat obsah pro analýzu.

> [!TIP]
> Text Analytics také poskytuje linuxovou image kontejneru Dockerpro detekci jazyka, takže můžete [nainstalovat a spustit kontejner Analýzy textu](text-analytics-how-to-install-containers.md) v blízkosti vašich dat.

Analýza mínění podporuje širokou škálu jazyků, s více ve verzi preview. Další informace najdete v tématu [Podporované jazyky](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Koncepty

Rozhraní API analýzy textu používá algoritmus klasifikace strojového učení ke generování skóre mínění mezi 0 a 1. Skóre blížící se 1 značí pozitivní mínění, zatímco skóre blížící se 0 značí negativní mínění. Analýza mínění se provádí u celého dokumentu namísto jednotlivých entit v textu. To znamená, že skóre mínění jsou vráceny na úrovni dokumentu nebo věty. 

Použitý model je předem trénovaný s rozsáhlým korpusem textových a sentimentových asociací. Využívá kombinaci technik pro analýzu, včetně zpracování textu, analýzy části řeči, umístění slov a přidružení slov. Další informace o tomto algoritmu najdete v [základních informacích o analýze textu](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/). V současné době není možné poskytnout vlastní trénovací data. 

Existuje tendence ke zlepšení přesnosti hodnocení, pokud dokumenty obsahují méně vět, nikoli velký blok textu. Během fáze hodnocení objektivity model určuje, jestli je dokument jako celek objektivní, nebo obsahuje subjektivní mínění. Dokument, který je většinou objektivní, nepostupuje do fáze zjišťování mínění, což má za následek skóre 0,50 bez dalšího zpracování. Pro dokumenty, které pokračují v kanálu, další fáze generuje skóre nad nebo pod 0,50. Skóre závisí na stupni mínění zjištěného v dokumentu.

## <a name="sentiment-analysis-versions-and-features"></a>Verze a funkce analýzy mínění

Rozhraní API pro analýzu textu nabízí dvě verze analýzy mínění – v2 a v3. Analýza mínění v3 (Public preview) poskytuje významná vylepšení v přesnosti a podrobnostech kategorizace textu rozhraní API a vyhodnocování.

> [!NOTE]
> * Formát požadavku analýzy mínění v3 a [omezení dat](../overview.md#data-limits) jsou stejné jako v předchozí verzi.
> * Analýza mínění v3 je k `Australia East`dispozici `Central Canada` `Central US`v `East Asia` `East US`následujících oblastech: `West Europe`, `West US 2`, , , `East US 2` `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, , , a .

| Funkce                                   | Analýza mínění v2 | Analýza mínění v3 |
|-------------------------------------------|-----------------------|-----------------------|
| Metody pro jednoduché a dávkové požadavky    | ×                     | ×                     |
| Skóre mínění pro celý dokument  | ×                     | ×                     |
| Skóre mínění pro jednotlivé věty |                       | ×                     |
| Označení mínění                        |                       | ×                     |
| Správa verzí modelu                   |                       | ×                     |

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

### <a name="sentiment-scoring"></a>Hodnocení mínění

Analýza mínění v3 klasifikuje text pomocí popisků mínění (popsaných níže). Vrácené skóre představují spolehlivost modelu, že text je buď kladný, negativní nebo neutrální. Vyšší hodnoty znamená vyšší důvěru. 

### <a name="sentiment-labeling"></a>Označení mínění

Analýza mínění v3 může vrátit skóre a popisky na úrovni věty a dokumentu. Skóre a popisky `negative`jsou `neutral` `positive`, a . Na úrovni dokumentu `mixed` popisek mínění také lze vrátit bez skóre. Mínění dokumentu je určeno níže:

| Věta sentiment                                                                            | Popisek vrácené dokumentu |
|-----------------------------------------------------------------------------------------------|-------------------------|
| V dokumentu `positive` je alespoň jedna věta. Zbytek věty jsou `neutral`. | `positive`              |
| V dokumentu `negative` je alespoň jedna věta. Zbytek věty jsou `neutral`. | `negative`              |
| V dokumentu `negative` je alespoň `positive` jedna věta a alespoň jedna věta.    | `mixed`                 |
| Všechny věty v `neutral`dokumentu jsou .                                                  | `neutral`               |

### <a name="model-versioning"></a>Správa verzí modelu

> [!NOTE]
> Správa verzí modelu pro analýzu `v3.0-preview.1`mínění je k dispozici od verze .

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>Příklad kódu Jazyka C#

Můžete najít příklad aplikace C#, která volá tuto verzi analýzy mínění na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/TextAnalyticsSentiment.cs).


#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

### <a name="sentiment-scoring"></a>Hodnocení mínění

Analyzátor mínění klasifikuje text jako převážně pozitivní nebo negativní. Přiřadí skóre v rozmezí 0:1. Hodnoty blížící se 0,5 představují neutrální nebo neurčité mínění. Skóre 0,5 indikuje neutralitu. Pokud řetězec nelze analyzovat pro sentiment nebo nemá žádný sentiment, skóre je vždy 0,5 přesně. Když například zadáte řetězec ve španělštině s kódem jazyka pro angličtinu, je skóre 0,5.

---

## <a name="sending-a-rest-api-request"></a>Odeslání požadavku rozhraní REST API 

### <a name="preparation"></a>Příprava

Analýza mínění vytváří vyšší kvalitu, když jí dáte menší množství textu, na kterých má pracovat. Je to tedy přesně naopak než u extrakce klíčových frází, která vrací lepší výsledky pro větší bloky textu. Zvažte podle toho možnost restrukturalizace vstupů, abyste z obou operací získali co nejlepší výsledky.

V tomto formátu musíte mít dokumenty JSON: ID, text a jazyk.

Velikost dokumentu musí být menší než 5 120 znaků na dokument. Můžete mít až 1 000 položek (ID) na kolekci. Kolekce se posílá v textu žádosti.

## <a name="structure-the-request"></a>Strukturujte požadavek

Vytvořte žádost POST. Můžete [použít Postman](text-analytics-how-to-call-api.md) nebo **testovací konzole rozhraní API** v následujících odkazech na odkazy rychle strukturovat a odeslat jeden. 

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

[Odkaz na analýzu mínění v3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

[Odkaz na analýzu mínění v2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

Nastavte koncový bod HTTPS pro analýzu mínění pomocí prostředku Analýzy textu v Azure nebo pomocí [kontejneru analýzy textu](text-analytics-how-to-install-containers.md)s instancemi . Je nutné zahrnout správnou adresu URL pro verzi, kterou chcete použít. Například:

> [!NOTE]
> Klíč a koncový bod pro prostředek Analýzy textu najdete na webu Azure Portal. Budou umístěny na stránce **rychlého startu** zdroje v části **Správa zdrojů**. 

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

Nastavte záhlaví požadavku tak, aby obsahovalo klíč rozhraní API pro analýzu textu. V textu požadavku zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

### <a name="example-sentiment-analysis-request"></a>Příklad požadavku analýzy mínění 

Následuje příklad obsahu, který můžete odeslat pro analýzu mínění. Formát požadavku je stejný pro obě verze rozhraní API.
    
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

### <a name="post-the-request"></a>Zaúčtovat žádost

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavků, které můžete odeslat za minutu a sekundu, naleznete v části [Omezení dat](../overview.md#data-limits) v přehledu.

Rozhraní API analýzy textu je bezstavové. Ve vašem účtu nejsou uložena žádná data a výsledky jsou okamžitě vráceny v odpovědi.


### <a name="view-the-results"></a>Zobrazení výsledků

Analyzátor mínění klasifikuje text jako převážně pozitivní nebo negativní. Přiřadí skóre v rozmezí 0:1. Hodnoty blížící se 0,5 představují neutrální nebo neurčité mínění. Skóre 0,5 indikuje neutralitu. Pokud řetězec nelze analyzovat pro sentiment nebo nemá žádný sentiment, skóre je vždy 0,5 přesně. Když například zadáte řetězec ve španělštině s kódem jazyka pro angličtinu, je skóre 0,5.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo uložit výstup do souboru v místním systému. Potom importujte výstup do aplikace, kterou můžete použít k řazení, vyhledávání a manipulaci s daty. Vzhledem k podpoře vícejazyčných a emoji může odpověď obsahovat posuny textu. Další informace naleznete [v tématu Zpracování posunů.](../concepts/text-offsets.md)

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>Příklad odpovědi analýzy mínění v3

Odpovědi z analýzy mínění v3 obsahují popisky mínění a skóre pro každou analyzovou větu a dokument. `documentScores`není vrácena, pokud je `mixed`popisek mínění dokumentu .

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

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>Analýza mínění v2 příklad odpovědi

Odpovědi z analýzy mínění v2 obsahují skóre mínění pro každý odeslaný dokument.

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

V tomto článku jste se dozvěděli koncepty a pracovní postupy pro analýzu mínění pomocí rozhraní API analýzy textu. Souhrn:

+ Analýza mínění je k dispozici pro vybrané jazyky ve dvou verzích.
+ Dokumenty JSON v textu požadavku obsahují ID, text a kód jazyka.
+ Požadavek POST je `/sentiment` ke koncovému bodu pomocí přizpůsobeného [přístupového klíče a koncového bodu,](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) který je platný pro vaše předplatné.
+ Výstup odpovědi, který se skládá ze skóre mínění pro každé ID dokumentu, lze streamovat do libovolné aplikace, která přijímá JSON. Například Excel a Power BI.

## <a name="see-also"></a>Viz také

* [Přehled analýzy textu](../overview.md)
* [Použití klientské knihovny Analýzy textu](../quickstarts/text-analytics-sdk.md)
* [Co je nového](../whats-new.md)
