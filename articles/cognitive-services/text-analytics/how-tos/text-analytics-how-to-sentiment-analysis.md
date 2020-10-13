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
ms.date: 10/09/2020
ms.author: aahi
ms.openlocfilehash: 570a21a307d60ab1e2c02d6481746576f5dcf0e3
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930284"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Postupy: zjištění mínění pomocí rozhraní API pro analýzu textu

Funkce Analýza mínění rozhraní API pro analýzu textu vyhodnotí text a vrátí mínění skóre a štítky pro každou větu. To je užitečné při zjišťování pozitivních a záporných mínění v sociálních médiích, na revizích zákazníků, diskuzních fórech a dalších. Modely AI používané rozhraním API jsou součástí služby, stačí ale odeslat obsah k analýze.

Po odeslání požadavku na analýzu mínění vrátí rozhraní API popisky mínění (například "negativní", "neutrální" a "pozitivní") a hodnocení spolehlivosti na větě a na úrovni dokumentu.

Analýza mínění podporuje široké spektrum jazyků, ve verzi Preview. Další informace najdete v části o [podporovaných jazycích](../text-analytics-supported-languages.md).

## <a name="sentiment-analysis-versions-and-features"></a>Analýza mínění verze a funkce

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Příznak                                   | Analýza mínění V3 | Analýza mínění v 3.1 (Preview) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Metody pro jednotlivé a dávkové požadavky    | X                     | X                                 |
| Mínění skóre a popisky             | X                     | X                                 |
| [Kontejner Docker](text-analytics-how-to-install-containers.md) založený na Linux | ×  |  |
| Dolování názoru                            |                       | ×                                 |

### <a name="sentiment-scoring-and-labeling"></a>Bodování a označování mínění

Analýza mínění v v3 aplikuje popisky mínění na text, které se vrátí na úrovni věty a dokumentu s hodnocením spolehlivosti pro každý z nich. 

Popisky jsou *kladné*, *záporné*a *neutrální*. Na úrovni dokumentu lze také vrátit *smíšený* popisek mínění. Mínění dokumentu je určena níže:

| Mínění věty                                                                            | Popisek vráceného dokumentu |
|-----------------------------------------------------------------------------------------------|-------------------------|
| `positive`V dokumentu je alespoň jedna věta. Zbytek vět je `neutral` . | `positive`              |
| `negative`V dokumentu je alespoň jedna věta. Zbytek vět je `neutral` . | `negative`              |
| `negative`V dokumentu je alespoň jedna věta a alespoň jedna `positive` věta.    | `mixed`                 |
| Všechny věty v dokumentu jsou `neutral` .                                                  | `neutral`               |

Výsledky spolehlivosti jsou v rozsahu od 1 do 0. Skóre Blíže k 1 znamenají vyšší důvěru v klasifikaci popisku, zatímco nižší skóre znamenají nižší důvěru. U každého dokumentu nebo každé věty jsou předpovězené skóre spojené s popisky (kladné, záporné a neutrální) přidány až 1.

### <a name="opinion-mining"></a>Dolování názoru

Dolování stanovisek je funkce Analýza mínění, počínaje verzí 3,1-Preview. 1. Tato funkce také označovaná jako Analýza mínění založená na aspektech při zpracování přirozeného jazyka (NLP). Tato funkce poskytuje podrobnější informace o stanoviscích souvisejících s aspekty (jako jsou atributy produktů nebo služeb) v textu.

Například pokud zákazník opustí svůj názor na Hotel, například "místnost byla Skvělé, ale zaměstnanci byli nepříteli", dolování názorů vyhledá aspekty v textu a jejich přidružená stanoviska a zabarvení:

| Aspekt | Řídícího    | Mínění |
|--------|------------|-----------|
| konverzační   | Skvělé      | pozitivní  |
| sazovat  | neznámých | negativní  |

Chcete-li získat výsledky dolování v rámci vašich výsledků, je nutné přidat `opinionMining=true` příznak do žádosti o analýzu mínění. Výsledky dolování výsledků budou zahrnuty v odpovědi na analýzu mínění.

## <a name="sending-a-rest-api-request"></a>Odesílá se žádost o REST API. 

### <a name="preparation"></a>Příprava

Analýza mínění přináší vyšší kvalitu, když dáte menší množství textu, na kterém bude pracovat. Je to tedy přesně naopak než u extrakce klíčových frází, která vrací lepší výsledky pro větší bloky textu. Pokud chcete mít v obou operacích optimální výsledky, měli byste uvažovat o změně struktury vstupů.

Je nutné mít dokumenty JSON v tomto formátu: ID, text a jazyk.

Velikost dokumentu musí být v rozmezí 5 120 znaků na dokumentu. Pro každou kolekci můžete mít až 1 000 položek (ID). Kolekce se posílá v textu žádosti.

## <a name="structure-the-request"></a>Strukturování žádosti

Vytvořte žádost POST. V následujících referenčních odkazech můžete [použít možnost post](text-analytics-how-to-call-api.md) nebo **Konzola pro testování API** k rychlému uspořádání a odeslání jednoho. 

#### <a name="version-31-preview1"></a>[Verze 3,1-Preview. 1](#tab/version-3-1)

[Referenční informace pro Analýza mínění v 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Sentiment)

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

[Reference Analýza mínění V3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>Koncové body požadavku

Nastavte koncový bod HTTPS pro analýzu mínění pomocí prostředku Analýza textu v Azure nebo vytvořeného [Analýza textu kontejneru](text-analytics-how-to-install-containers.md). Musíte zahrnout správnou adresu URL pro verzi, kterou chcete použít. Například:

> [!NOTE]
> Klíč a koncový bod pro váš Analýza textu prostředek najdete na webu Azure Portal. Budou se nacházet na stránce **rychlý Start** prostředku v části **Správa prostředků**. 

#### <a name="version-31-preview1"></a>[Verze 3,1-Preview. 1](#tab/version-3-1)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment`

Chcete-li získat názory výsledků dolování, je nutné zahrnout `opinionMining=true` parametr. Například:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment?opinionMining=true`

Tento parametr je ve výchozím nastavení nastaven na hodnotu `false` . 

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

Nastavte hlavičku požadavku tak, aby obsahovala klíč rozhraní API pro analýzu textu. V textu požadavku zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

### <a name="example-sentiment-analysis-request"></a>Příklad Analýza mínění žádosti 

Následuje příklad obsahu, který můžete odeslat pro analýzu mínění. Formát požadavku je pro obě verze stejný.
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>Publikování žádosti

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavků, které můžete poslat za minutu a sekundu, najdete v části [omezení dat](../overview.md#data-limits) v přehledu.

Rozhraní API pro analýzu textu je Bezstavová. Ve vašem účtu se neukládají žádná data a výsledky se vrátí hned v odpovědi.


### <a name="view-the-results"></a>Zobrazení výsledků

Mínění Analysis vrátí popisek mínění a hodnocení spolehlivosti pro celý dokument a každou větu v něm. Skóre Blíže k 1 znamenají vyšší důvěru v klasifikaci popisku, zatímco nižší skóre znamenají nižší důvěru. Dokument může mít několik vět a hodnocení spolehlivosti v jednotlivých dokumentech nebo větách přidalo až 1.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo uložit výstup do souboru v místním systému. Pak importujte výstup do aplikace, kterou můžete použít k řazení, vyhledávání a manipulaci s daty. Vzhledem k podpoře vícejazyčných a Emoji může odpověď obsahovat posunutí textu. Další informace najdete v tématu [postup zpracování posunů](../concepts/text-offsets.md) .

#### <a name="version-31-preview1"></a>[Verze 3,1-Preview. 1](#tab/version-3-1)

### <a name="sentiment-analysis-v31-example-response"></a>Příklad odpovědi Analýza mínění v 3.1

Analýza mínění v 3.1 nabízí kromě objektu Response na kartě **verze 3,0** i další názory a dolování. V níže uvedené reakci je věta, *že má restaurace skvělou stravu a že náš čekací chvilku byl srozumitelný* , má dva aspekty: *jídlo* a *čekací*služba. Vlastnost každého aspektu `relations` obsahuje `ref` hodnotu s odkazem na identifikátor URI na asociované `documents` objekty, `sentences` a `opinions` .

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly.",
                    "aspects": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 25,
                            "length": 4,
                            "text": "food",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/0"
                                }
                            ]
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 38,
                            "length": 6,
                            "text": "waiter",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/1"
                                }
                            ]
                        }
                    ],
                    "opinions": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 19,
                            "length": 5,
                            "text": "great",
                            "isNegated": false
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 49,
                            "length": 8,
                            "text": "friendly",
                            "isNegated": false
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

### <a name="sentiment-analysis-v30-example-response"></a>Příklad odpovědi Analýza mínění v 3.0

Odpovědi z Analýza mínění V3 obsahují mínění popisky a skóre pro každou analyzovanou větu a dokument.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

---

## <a name="summary"></a>Shrnutí

V tomto článku jste zjistili koncepty a pracovní postup pro analýzu mínění pomocí rozhraní API pro analýzu textu. Souhrn:

+ Analýza mínění je k dispozici pro vybrané jazyky.
+ Dokumenty JSON v textu požadavku zahrnují ID, text a kód jazyka.
+ Požadavek POST je na `/sentiment` koncový bod pomocí přizpůsobeného [přístupového klíče a koncového bodu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , který je platný pro vaše předplatné.
+ Výstup odpovědi, který se skládá z mínění skóre pro každé ID dokumentu, se může streamovat do libovolné aplikace, která přijímá JSON. Například Excel a Power BI.

## <a name="see-also"></a>Viz také

* [Přehled analýzy textu](../overview.md)
* [Použití klientské knihovny Analýza textu](../quickstarts/text-analytics-sdk.md)
* [Co je nového](../whats-new.md)
