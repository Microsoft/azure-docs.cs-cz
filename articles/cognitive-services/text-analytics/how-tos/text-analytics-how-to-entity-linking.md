---
title: Použití rozpoznávání entit s rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak identifikovat a odstranit identitu entity, která se nachází v textu s Analýza textu REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: 3fd3695490331a1f599db71bf5cafb25e957bf08
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710341"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Jak používat rozpoznávání pojmenovaných entit v Analýza textu

Rozhraní API pro analýzu textu umožňuje přebírá nestrukturovaný text a vrací seznam nejednoznačných entit s odkazy na Další informace na webu. Rozhraní API podporuje rozpoznávání pojmenovaných entit (NER) pro několik kategorií entit a propojení entit.

## <a name="entity-linking"></a>Entity Linking

Propojení entit je schopnost identifikovat a odstranit identitu entity nalezenou v textu (například určit, zda výskyt slova "Mars" odkazuje na globálním nebo římské jsou války). Tento proces vyžaduje přítomnost znalostní báze v příslušném jazyce, aby bylo možné propojit rozpoznané entity v textu. Při propojování entit se jako tato znalostní báze používá [Wikipedii](https://www.wikipedia.org/) .

## <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

Rozpoznávání pojmenovaných entit (NER) je schopnost identifikovat různé entity v textu a kategorizovat je do předem definovaných tříd nebo typů, jako je například osoba, umístění, událost, produkt a organizace.  

## <a name="personally-identifiable-information-pii"></a>Identifikovatelné osobní údaje (PII)

Funkce PII je součástí NER a může identifikovat a redigování citlivé entity v textu, který je spojený s jednotlivou osobou, jako je telefonní číslo, e-mailová adresa, poštovní adresa, číslo služby Passport.

## <a name="named-entity-recognition-features-and-versions"></a>Funkce a verze nástroje pro rozpoznávání pojmenovaných entit

| Funkce                                                         | NER v 3.0 | NER v 3.1 – Preview. 3 |
|-----------------------------------------------------------------|--------|----------|
| Metody pro jednotlivé a dávkové požadavky                          | ×      | ×        |
| Rozšířené rozpoznávání entit napříč několika kategoriemi           | ×      | ×        |
| Samostatné koncové body pro posílání NER entit a žádostí o připojení. | ×      | ×        |
| Rozpoznávání osobních entit ( `PII` ) a `PHI` informací o stavu ()        |        | ×        |
| Redigování `PII`        |        | ×        |

Informace najdete v tématu [Podpora jazyků](../language-support.md) .

Rozpoznávání pojmenovaných entit V3 poskytuje rozšířené zjišťování napříč více typy. V současné době NER v 3.0 dokáže rozpoznat entity v [kategorii obecné entity](../named-entity-types.md).

Recognitioned entity Recognition v 3.1-Preview. 3 zahrnuje možnosti detekce v 3.0 a: 
* Schopnost zjišťovat osobní údaje ( `PII` ) pomocí `v3.1-preview.3/entities/recognition/pii` koncového bodu. 
* Volitelný `domain=phi` parametr pro detekci důvěrných informací o stavu ( `PHI` ).
* [Asynchronní operace](text-analytics-how-to-call-api.md) s použitím `/analyze` koncového bodu.

Další informace najdete v oddílu [kategorie entit](../named-entity-types.md) a níže v části [požadavky na koncové body](#request-endpoints) . Další informace o konfidenčních hodnoceních najdete v části [Analýza textu transparentnosti poznámky](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context). 

## <a name="sending-a-rest-api-request"></a>Odesílá se žádost o REST API.

### <a name="preparation"></a>Příprava

Je nutné mít dokumenty JSON v tomto formátu: ID, text, jazyk.

Každý dokument musí být pod 5 120 znaky a pro každou kolekci můžete mít až 1 000 položek (ID). Kolekce se posílá v textu žádosti.

### <a name="structure-the-request"></a>Strukturování žádosti

Vytvořte žádost POST. Můžete [použít post](text-analytics-how-to-call-api.md) nebo **konzolu testování API** v následujících odkazech k rychlému uspořádání a odeslání jednoho. 

> [!NOTE]
> Klíč a koncový bod pro váš Analýza textu prostředek najdete na webu Azure Portal. Budou se nacházet na stránce **rychlý Start** prostředku v části **Správa prostředků**. 


### <a name="request-endpoints"></a>Koncové body požadavku

#### <a name="version-31-preview3"></a>[Verze 3,1-Preview. 3](#tab/version-3-preview)

Rozpoznávání pojmenovaných entit `v3.1-preview.3` používá samostatné koncové body pro žádosti o propojení ner, PII a entit. V závislosti na vaší žádosti použijte formát adresy URL.

**Propojení entit**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/linking`

[Verze rozpoznávání pojmenovaných entit verze 3,1-Preview pro `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesLinking)

**Rozpoznávání pojmenovaných entit**
* Obecné entity – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/general`

[Verze rozpoznávání pojmenovaných entit verze 3,1-Preview pro `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionGeneral)

**Identifikovatelné osobní údaje (PII)**
* Osobní ( `PII` ) informace – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii`

`domain=phi`K detekci `PHI` informací o stavu () v textu můžete použít také volitelný parametr. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi`

Počínaje `v3.1-preview.3` verzí odpověď JSON obsahuje `redactedText` vlastnost, která obsahuje upravený vstupní text, kde byly zjištěné entity, které byly zjištěny, nahrazeny hodnotou `*` pro každý znak v entitách.

[Verze rozpoznávání pojmenovaných entit verze 3,1-Preview pro `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionPii)

**Asynchronní operace**

Počínaje `v3.1-preview.3` nástrojem můžete odesílat požadavky ner asynchronně pomocí `/analyze` koncového bodu.

* Asynchronní operace – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze`

Informace o posílání asynchronních požadavků naleznete v tématu [How to Call the rozhraní API pro analýzu textu](text-analytics-how-to-call-api.md) .

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Rozpoznávání pojmenovaných entit V3 používá samostatné koncové body pro žádosti NER a propojení entit. V závislosti na vaší žádosti použijte formát adresy URL:

**Propojení entit**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[Referenční informace k verzi pro rozpoznávání pojmenované entity 3,0 `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

**Rozpoznávání pojmenovaných entit**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[Referenční informace k verzi pro rozpoznávání pojmenované entity 3,0 `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

Nastavte hlavičku požadavku tak, aby obsahovala klíč rozhraní API pro analýzu textu. V textu žádosti zadejte dokumenty JSON, které jste připravili.

## <a name="example-requests"></a>Příklady požadavků

#### <a name="version-31-preview"></a>[Verze 3,1-Preview](#tab/version-3-preview)

### <a name="example-synchronous-ner-request"></a>Příklad synchronní žádosti NER 

Následující JSON je příkladem obsahu, který můžete odeslat do rozhraní API. Formát požadavku je pro obě verze rozhraní API stejný.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

### <a name="example-asynchronous-ner-request"></a>Příklad asynchronní žádosti NER

Použijete-li `/analyze` koncový bod pro [asynchronní operaci](text-analytics-how-to-call-api.md), dostanete odpověď obsahující úkoly, které jste odeslali do rozhraní API.

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
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}
```

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

### <a name="example-synchronous-ner-request"></a>Příklad synchronní žádosti NER 

Verze 3,0 obsahuje pouze synchronní operaci. Následující JSON je příkladem obsahu, který můžete odeslat do rozhraní API. Formát požadavku je pro obě verze rozhraní API stejný.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

---

## <a name="post-the-request"></a>Publikování žádosti

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavků, které můžete odeslat za minutu a sekundy, najdete v části [omezení dat](../overview.md#data-limits) v přehledu.

Rozhraní API pro analýzu textu je Bezstavová. Ve vašem účtu se neukládají žádná data a výsledky se vrátí hned v odpovědi.

## <a name="view-results"></a>Zobrazení výsledků

Všechny žádosti POST vrátí odpověď ve formátu JSON s ID a zjištěnými vlastnostmi entity.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo můžete výstup uložit do souboru v místním systému a potom ho naimportovat do aplikace, která umožňuje řadit a vyhledávat data a pracovat s nimi. Vzhledem k podpoře vícejazyčných a Emoji může odpověď obsahovat posunutí textu. Další informace najdete v tématu [postup zpracování posunutí textu](../concepts/text-offsets.md).

### <a name="example-responses"></a>Příklady odpovědí

Verze 3 poskytuje samostatné koncové body pro obecné NER, PII a propojení entit. Verze 3,1 – pareview obsahuje režim asynchronní analýzy. Odpovědi na tyto operace jsou uvedené níže. 

#### <a name="version-31-preview"></a>[Verze 3,1-Preview](#tab/version-3-preview)

### <a name="synchronous-example-results"></a>Synchronní příklady výsledků

Příklad obecné odpovědi NER:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

Příklad odpovědi PII:

```json
{
  "documents": [
    {
    "redactedText": "You can even pre-order from their online menu at *************************, call ************ or send email to ***************************!",
    "id": "0",
    "entities": [
        {
        "text": "www.contososteakhouse.com",
        "category": "URL",
        "offset": 49,
        "length": 25,
        "confidenceScore": 0.8
        }, 
        {
        "text": "312-555-0176",
        "category": "Phone Number",
        "offset": 81,
        "length": 12,
        "confidenceScore": 0.8
        }, 
        {
        "text": "order@contososteakhouse.com",
        "category": "Email",
        "offset": 111,
        "length": 27,
        "confidenceScore": 0.8
        }
      ],
    "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-07-01"
}
```

Příklad odpovědi na propojení entity:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```

### <a name="example-asynchronous-result"></a>Příklad asynchronního výsledku

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


#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Příklad obecné odpovědi NER:
```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
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


## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postupy pro propojení entit pomocí Analýza textu v Cognitive Services. Souhrn:

* Dokumenty JSON v textu požadavku zahrnují ID, text a kód jazyka.
* Žádosti POST se odesílají do jednoho nebo více koncových bodů s použitím přizpůsobeného [přístupového klíče a koncového bodu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , který je platný pro vaše předplatné.
* Výstup odpovědi, který se skládá z propojených entit (včetně hodnocení spolehlivosti, posunu a webových odkazů, pro každé ID dokumentu), se dá použít v libovolné aplikaci.

## <a name="next-steps"></a>Další kroky

* [Přehled analýzy textu](../overview.md)
* [Použití klientské knihovny Analýza textu](../quickstarts/client-libraries-rest-api.md)
* [Co je nového](../whats-new.md)
