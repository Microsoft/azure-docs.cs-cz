---
title: Extrakce klíčových frází pomocí Analýza textu REST API
titleSuffix: Azure Cognitive Services
description: Postup extrakce klíčových frází pomocí Analýza textu REST API z Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: 91e10c25d2c3bef9c1ca20e3e5737a326d45997c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "97654774"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Příklad: jak extrahovat klíčové fráze pomocí Analýza textu

[Rozhraní API pro extrakci klíčových frází](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) vyhodnotí nestrukturovaný text a pro každý dokument JSON vrátí seznam klíčových frází.

Tato funkce je užitečná v případě, kdy potřebujete rychle identifikovat hlavní body v kolekci dokumentů. Například pro vstupní text „The food was delicious and there were wonderful staff“ (Jídlo bylo výborné a personál byl úžasný), vrátí služba hlavní body: „food“ (jídlo) a „wonderful staff“ (úžasný personál).

Další informace najdete v části o [podporovaných jazycích](../language-support.md).

> [!TIP]
> * Analýza textu taky poskytuje pro extrakci klíčových frází image kontejneru Docker pro Linux, takže můžete [nainstalovat a spustit kontejner analýza textu](text-analytics-how-to-install-containers.md) blízko k datům.
> * Tuto funkci můžete také použít [asynchronně](text-analytics-how-to-call-api.md) pomocí `/analyze` koncového bodu.

## <a name="preparation"></a>Příprava

Extrakce klíčových frází funguje nejlépe, když jim dáte větší množství textu, na kterém chcete pracovat. To je opakem analýzy mínění, která je vhodnější pro menší množství textu. Pokud chcete mít v obou operacích optimální výsledky, měli byste uvažovat o změně struktury vstupů.

Je nutné mít dokumenty JSON v tomto formátu: ID, text, jazyk

Velikost dokumentu musí být 5 120 nebo méně znaků v jednom dokumentu a pro každou kolekci můžete mít až 1 000 položek (ID). Kolekce se posílá v textu žádosti. Následující příklad ilustruje obsah, který byste mohli odeslat za účelem extrakce klíčových frází. 

Další informace o objektech požadavků a odpovědí naleznete v tématu [How to call rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md) .  

### <a name="example-synchronous-request-object"></a>Příklad objektu synchronní žádosti


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

### <a name="example-asynchronous-request-object"></a>Příklad objektu asynchronního požadavku

Počínaje `v3.1-preview.3` nástrojem můžete odesílat požadavky ner asynchronně pomocí `/analyze` koncového bodu.


```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
    }
}
```

## <a name="step-1-structure-the-request"></a>Krok 1: Struktura žádosti

Informace o definici požadavku naleznete v tématu [způsob volání rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md). Pro usnadnění znovu uvádíme následující body:

+ Vytvořte žádost **POST**. Přečtěte si dokumentaci k rozhraní API pro tento požadavek: [klíčové rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases).

+ Nastavte koncový bod HTTP pro extrakci klíčových frází pomocí prostředku Analýza textu v Azure nebo vytvořeného [Analýza textu kontejneru](text-analytics-how-to-install-containers.md). Pokud rozhraní API používáte synchronně, musíte zahrnout `/text/analytics/v3.0/keyPhrases` do adresy URL. Příklad: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`.

+ Nastavte hlavičku požadavku tak, aby obsahovala [přístupový klíč](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) pro operace analýza textu.

+ V textu požadavku zadejte kolekci dokumentů JSON, kterou jste si připravili pro tuto analýzu.

> [!Tip]
> Použijte aplikaci [Postman](text-analytics-how-to-call-api.md) nebo otevřete **konzolu pro testování rozhraní API** v [dokumentaci](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) a vytvořte strukturu žádosti a pomocí příkazu POST ji odešlete do služby.

## <a name="step-2-post-the-request"></a>Krok 2: Odeslání žádosti

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavků, které můžete odeslat za minutu nebo za sekundu, najdete v části [omezení dat](../overview.md#data-limits) v přehledu.

Nezapomeňte, že služba je bezstavová. Ve vašem účtu se neukládají žádná data. Výsledky se vrátí okamžitě v odpovědi.

## <a name="step-3-view-results"></a>Krok 3: Zobrazení výsledků

Všechny žádosti POST vrací odpověď ve formátu JSON s ID a zjištěnými vlastnostmi. Pořadí vrácených klíčových frází je určeno interně podle modelu.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo můžete výstup uložit do souboru v místním systému a potom ho naimportovat do aplikace, která umožňuje řadit a vyhledávat data a pracovat s nimi.

Příklad výstupu pro extrakci klíčových frází z koncového bodu verze 3.1-Preview. 2 je uveden zde:

### <a name="synchronous-result"></a>Synchronní výsledek

```json
    {
       "documents":[
          {
             "id":"1",
             "keyPhrases":[
                "year",
                "trail",
                "trip",
                "views",
                "hike"
             ],
             "warnings":[]
          },
          {
             "id":"2",
             "keyPhrases":[
                "marked trails",
                "Worst hike",
                "goners"
             ],
             "warnings":[]
          },
          {
             "id":"3",
             "keyPhrases":[
                "trail",
                "small children",
                "family"
             ],
             "warnings":[]
          },
          {
             "id":"4",
             "keyPhrases":[
                "spectacular views",
                "trail",
                "Worth",
                "area"
             ],
             "warnings":[]
          },
          {
             "id":"5",
             "keyPhrases":[
                "places",
                "beautiful views",
                "favorite trail",
                "rest"
             ],
             "warnings":[]
          }
       ],
       "errors":[],
       "modelVersion":"2020-07-01"
    }

```
Jak je uvedeno, analyzátor vyhledá a zahodí nepostradatelná slova a udržuje jednoduché termíny nebo fráze, které se jeví jako předmět nebo předmět věty.

### <a name="asynchronous-result"></a>Asynchronní výsledek

Použijete-li `/analyze` koncový bod pro asynchronní operaci, dostanete odpověď obsahující úkoly, které jste odeslali do rozhraní API.

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postup pro extrakci klíčových frází pomocí Analýza textu v Cognitive Services. Souhrn:

+ [Rozhraní API pro extrakci klíčových frází](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) je k dispozici pro vybrané jazyky.
+ Dokumenty JSON v textu požadavku zahrnují ID, text a kód jazyka.
+ Požadavek POST je na `/keyphrases` `/analyze` koncový bod nebo s použitím přizpůsobeného [přístupového klíče a koncového bodu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , který je platný pro vaše předplatné.
+ Výstup odpovědi, který se skládá z klíčových slov a frází pro každé ID dokumentu, se může streamovat do libovolné aplikace, která přijímá JSON, včetně systém Microsoft Office Excel a Power BI, pro pojmenování.

## <a name="see-also"></a>Viz také

 [Analýza textu přehled](../overview.md) [častých otázek (FAQ)](../text-analytics-resource-faq.md)</br>
 [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Další kroky

* [Přehled analýzy textu](../overview.md)
* [Použití klientské knihovny Analýza textu](../quickstarts/client-libraries-rest-api.md)
* [Co je nového](../whats-new.md)
