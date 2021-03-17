---
title: Mínění analýzy a hodnocení názorů pomocí Analýza textu REST API
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak zjistit, jak mínění, tak o stanoviscích v textu pomocí rozhraní API pro analýzu textu Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 12/04/2020
ms.author: aahi
ms.openlocfilehash: 6ea7b992a682537471ce0e78385b37674199d687
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673049"
---
# <a name="how-to-sentiment-analysis-and-opinion-mining"></a>Postupy: analýza dolování mínění a jejich názoru

Funkce Analýza mínění rozhraní API pro analýzu textu poskytuje dva způsoby, jak zjistit pozitivní a negativní mínění. Pokud odešlete žádost o Analýza mínění, rozhraní API vrátí mínění popisky (například "negativní", "neutrální" a "pozitivní") a hodnocení spolehlivosti na větě a na úrovni dokumentu. Můžete také odesílat názory na požadavky na dolování pomocí Analýza míněního koncového bodu, který poskytuje podrobné informace o stanoviscích souvisejících s aspekty (jako jsou atributy produktů nebo služeb) v textu. 

Modely AI používané rozhraním API jsou součástí služby, stačí ale odeslat obsah k analýze.

## <a name="sentiment-analysis-versions-and-features"></a>Analýza mínění verze a funkce

| Příznak                                   | Analýza mínění V3 | Analýza mínění v 3.1 (Preview) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Metody pro jednotlivé a dávkové požadavky    | X                     | X                                 |
| Analýza mínění skóre a označování             | X                     | X                                 |
| [Kontejner Docker](text-analytics-how-to-install-containers.md) založený na Linux | X  |  |
| Dolování názoru                            |                       | X                                 |

## <a name="sentiment-analysis"></a>Analýza mínění

Analýza mínění ve verzi 3. x aplikuje popisky mínění na text, které se vrátí na úrovni věty a dokumentu s hodnocením spolehlivosti pro každý z nich. 

Popisky jsou *kladné*, *záporné* a *neutrální*. Na úrovni dokumentu lze také vrátit *smíšený* popisek mínění. Mínění dokumentu je určena níže:

| Mínění věty                                                                            | Popisek vráceného dokumentu |
|-----------------------------------------------------------------------------------------------|-------------------------|
| `positive`V dokumentu je alespoň jedna věta. Zbytek vět je `neutral` . | `positive`              |
| `negative`V dokumentu je alespoň jedna věta. Zbytek vět je `neutral` . | `negative`              |
| `negative`V dokumentu je alespoň jedna věta a alespoň jedna `positive` věta.    | `mixed`                 |
| Všechny věty v dokumentu jsou `neutral` .                                                  | `neutral`               |

Výsledky spolehlivosti jsou v rozsahu od 1 do 0. Skóre Blíže k 1 znamenají vyšší důvěru v klasifikaci popisku, zatímco nižší skóre znamenají nižší důvěru. U každého dokumentu nebo každé věty jsou předpovězené skóre spojené s popisky (kladné, záporné a neutrální) přidány až 1. Další informace najdete v [poznámce transparentnosti analýza textu](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context). 

## <a name="opinion-mining"></a>Dolování názoru

Dolování stanovisek je funkce Analýza mínění, počínaje verzí Preview verze 3,1. Tato funkce také označovaná jako Analýza mínění založená na aspektech při zpracování přirozeného jazyka (NLP). Tato funkce poskytuje podrobnější informace o stanoviscích souvisejících s aspekty (jako jsou atributy produktů nebo služeb) v textu.

Například pokud zákazník opustí svůj názor na Hotel, například "místnost byla Skvělé, ale zaměstnanci byli nepříteli.", dolování názorů vyhledá aspekty v textu a jejich přidružená stanoviska a zabarvení. Analýza mínění může hlásit pouze negativní mínění.

:::image type="content" source="../media/how-tos/opinion-mining.png" alt-text="Diagram příkladu dolování podle názoru" lightbox="../media/how-tos/opinion-mining.png":::

Chcete-li získat výsledky dolování v rámci vašich výsledků, je nutné přidat `opinionMining=true` příznak do žádosti o analýzu mínění. Výsledky dolování výsledků budou zahrnuty v odpovědi na analýzu mínění. Dolování stanovisek je rozšířením Analýza mínění a je součástí vaší aktuální [cenové úrovně](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).


## <a name="sending-a-rest-api-request"></a>Odesílá se žádost o REST API. 

### <a name="preparation"></a>Příprava

Analýza mínění přináší vyšší kvalitu, když dáte menší množství textu, na kterém bude pracovat. Je to tedy přesně naopak než u extrakce klíčových frází, která vrací lepší výsledky pro větší bloky textu. Pokud chcete mít v obou operacích optimální výsledky, měli byste uvažovat o změně struktury vstupů.

Je nutné mít dokumenty JSON v tomto formátu: ID, text a jazyk. Analýza mínění podporuje široké spektrum jazyků, ve verzi Preview. Další informace najdete v části o [podporovaných jazycích](../language-support.md).

Velikost dokumentu musí být v rozmezí 5 120 znaků na dokumentu. Maximální počet dokumentů povolených v kolekci najdete v článku o [omezeních dat](../concepts/data-limits.md?tabs=version-3) v tématu Koncepty. Kolekce se posílá v textu žádosti.

## <a name="structure-the-request"></a>Strukturování žádosti

Vytvořte žádost POST. V následujících referenčních odkazech můžete [použít možnost post](text-analytics-how-to-call-api.md) nebo **Konzola pro testování API** k rychlému uspořádání a odeslání jednoho. 

#### <a name="version-31-preview3"></a>[Verze 3,1-Preview. 3](#tab/version-3-1)

[Referenční informace pro Analýza mínění v 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

[Reference Analýza mínění V3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>Koncové body požadavku

Nastavte koncový bod HTTPS pro analýzu mínění pomocí prostředku Analýza textu v Azure nebo vytvořeného [Analýza textu kontejneru](text-analytics-how-to-install-containers.md). Musíte zahrnout správnou adresu URL pro verzi, kterou chcete použít. Příklad:

> [!NOTE]
> Klíč a koncový bod pro váš Analýza textu prostředek můžete najít na Azure Portal. Budou se nacházet na stránce **rychlý Start** prostředku v části **Správa prostředků**. 

#### <a name="version-31-preview3"></a>[Verze 3,1-Preview. 3](#tab/version-3-1)

**Analýza mínění**

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/sentiment`

**Dolování názoru**

Chcete-li získat názory výsledků dolování, je nutné zahrnout `opinionMining=true` parametr. Příklad:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/sentiment?opinionMining=true`

Tento parametr je ve výchozím nastavení nastaven na hodnotu `false` . 

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

**Analýza mínění**

V v 3.0 je dostupný jenom koncový bod pro Analýza mínění.
 
`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

Nastavte hlavičku požadavku tak, aby obsahovala klíč rozhraní API pro analýzu textu. V textu požadavku zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

### <a name="example-request-for-sentiment-analysis-and-opinion-mining"></a>Příklad žádosti o dolování Analýza mínění a názoru  

Následuje příklad obsahu, který můžete odeslat pro analýzu mínění. Formát požadavku je stejný pro i `v3.0` `v3.1-preview` .
    
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

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo uložit výstup do souboru v místním systému. Pak importujte výstup do aplikace, kterou můžete použít k řazení, vyhledávání a manipulaci s daty. Vzhledem k podpoře vícejazyčných a Emoji může odpověď obsahovat posunutí textu. Další informace najdete v tématu [postup zpracování posunů](../concepts/text-offsets.md) .

#### <a name="version-31-preview3"></a>[Verze 3,1-Preview. 3](#tab/version-3-1)

### <a name="sentiment-analysis-and-opinion-mining-example-response"></a>Příklad odpovědi na příklad dolování Analýza mínění a názoru

> [!IMPORTANT]
> Následuje příklad formátu JSON pro použití dolování s Analýza mínění, který je nabízen v systému v 3.1 rozhraní API. Pokud nepožadujete dolování v žádosti, bude odpověď rozhraní API stejná jako na kartě **verze 3,0** .  

Analýza mínění v 3.1 může vracet objekty odpovědi pro dolování Analýza mínění a stanovisek.
  
Mínění Analysis vrátí popisek mínění a hodnocení spolehlivosti pro celý dokument a každou větu v něm. Skóre Blíže k 1 znamenají vyšší důvěru v klasifikaci popisku, zatímco nižší skóre znamenají nižší důvěru. Dokument může mít několik vět a hodnocení spolehlivosti v jednotlivých dokumentech nebo větách přidalo až 1.

Dolování podle názoru vyhledá v textu aspekty a jejich související stanoviska a zabarveníy. V níže uvedené reakci je věta, *že má restaurace skvělou stravu a že náš čekací chvilku byl srozumitelný* , má dva aspekty: *jídlo* a *čekací* služba. Vlastnost každého aspektu `relations` obsahuje `ref` hodnotu s odkazem na identifikátor URI na asociované `documents` objekty, `sentences` a `opinions` .

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

### <a name="sentiment-analysis-example-response"></a>Analýza mínění příklad odpovědi

Mínění Analysis vrátí popisek mínění a hodnocení spolehlivosti pro celý dokument a každou větu v něm. Skóre Blíže k 1 znamenají vyšší důvěru v klasifikaci popisku, zatímco nižší skóre znamenají nižší důvěru. Dokument může mít několik vět a hodnocení spolehlivosti v jednotlivých dokumentech nebo větách přidalo až 1.

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

+ Pro vybrané jazyky je k dispozici Analýza mínění a k dolování stanovisek.
+ Dokumenty JSON v textu požadavku zahrnují ID, text a kód jazyka.
+ Požadavek POST je na `/sentiment` koncový bod pomocí přizpůsobeného [přístupového klíče a koncového bodu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , který je platný pro vaše předplatné.
+ Využijte `opinionMining=true` v žádostech o analýzu sentient k získání názoru na výsledky dolování.
+ Výstup odpovědi, který se skládá z mínění skóre pro každé ID dokumentu, se může streamovat do libovolné aplikace, která přijímá JSON. Například Excel a Power BI.

## <a name="see-also"></a>Viz také

* [Přehled analýzy textu](../overview.md)
* [Použití klientské knihovny Analýza textu](../quickstarts/client-libraries-rest-api.md)
* [Co je nového](../whats-new.md)
