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
ms.date: 09/24/2020
ms.author: aahi
ms.openlocfilehash: 5f5122b5fa7c20bc0717ef1605e41bb5f2700be2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91309094"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Jak používat rozpoznávání pojmenovaných entit v Analýza textu

Rozhraní API pro analýzu textu umožňuje přebírá nestrukturovaný text a vrací seznam nejednoznačných entit s odkazy na Další informace na webu. Rozhraní API podporuje rozpoznávání pojmenovaných entit (NER) i propojení entit.

### <a name="entity-linking"></a>Entity Linking

Propojení entit je schopnost identifikovat a odstranit identitu entity nalezenou v textu (například určit, zda výskyt slova "Mars" odkazuje na globálním nebo římské jsou války). Tento proces vyžaduje přítomnost znalostní báze v příslušném jazyce, aby bylo možné propojit rozpoznané entity v textu. Při propojování entit se jako tato znalostní báze používá [Wikipedii](https://www.wikipedia.org/) .


### <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

Rozpoznávání pojmenovaných entit (NER) je schopnost identifikovat různé entity v textu a kategorizovat je do předem definovaných tříd nebo typů, jako je například osoba, umístění, událost, produkt a organizace.  

## <a name="named-entity-recognition-versions-and-features"></a>Verze a funkce pro rozpoznávání pojmenovaných entit

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Příznak                                                         | NER v 3.0 | NER verze 3.1 – Preview. 2 |
|-----------------------------------------------------------------|--------|----------|
| Metody pro jednotlivé a dávkové požadavky                          | X      | X        |
| Rozšířené rozpoznávání entit napříč několika kategoriemi           | X      | X        |
| Samostatné koncové body pro posílání NER entit a žádostí o připojení. | X      | X        |
| Rozpoznávání osobních entit ( `PII` ) a `PHI` informací o stavu ()        |        | X        |

Informace najdete v tématu [Podpora jazyků](../language-support.md) .

### <a name="entity-types"></a>Typy entit

Rozpoznávání pojmenovaných entit V3 poskytuje rozšířené zjišťování napříč více typy. V současné době NER v 3.0 dokáže rozpoznat entity v [kategorii obecné entity](../named-entity-types.md).

Rozpoznávání pojmenovaných entit v 3.1-Preview. 2 zahrnuje možnosti detekce v 3.0 a možnost detekovat osobní údaje ( `PII` ) pomocí `v3.1-preview.2/entities/recognition/pii` koncového bodu. `domain=phi`K detekci důvěrných informací o stavu () můžete použít volitelný parametr `PHI` . Další informace najdete níže v části věnované předmětům [kategorie](../named-entity-types.md) a [koncovým bodům žádosti](#request-endpoints) .


## <a name="sending-a-rest-api-request"></a>Odesílá se žádost o REST API.

### <a name="preparation"></a>Příprava

Je nutné mít dokumenty JSON v tomto formátu: ID, text, jazyk.

Každý dokument musí být pod 5 120 znaky a pro každou kolekci můžete mít až 1 000 položek (ID). Kolekce se posílá v textu žádosti.

### <a name="structure-the-request"></a>Strukturování žádosti

Vytvořte žádost POST. Můžete [použít post](text-analytics-how-to-call-api.md) nebo **konzolu testování API** v následujících odkazech k rychlému uspořádání a odeslání jednoho. 

> [!NOTE]
> Klíč a koncový bod pro váš Analýza textu prostředek najdete na webu Azure Portal. Budou se nacházet na stránce **rychlý Start** prostředku v části **Správa prostředků**. 


### <a name="request-endpoints"></a>Koncové body požadavku

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

Rozpoznávání pojmenovaných entit V3 používá samostatné koncové body pro žádosti NER a propojení entit. V závislosti na vaší žádosti použijte formát adresy URL:

Propojení entit
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[Referenční informace k verzi pro rozpoznávání pojmenované entity 3,0 `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[Referenční informace k verzi pro rozpoznávání pojmenované entity 3,0 `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

#### <a name="version-31-preview2"></a>[Verze 3,1-Preview. 2](#tab/version-3-preview)

Rozpoznávání pojmenovaných entit `v3.1-preview.2` používá samostatné koncové body pro žádosti ner a propojení entit. V závislosti na vaší žádosti použijte formát adresy URL:

Propojení entit
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/linking`

[Verze rozpoznávání pojmenovaných entit verze 3,1-Preview pro `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking)

NER
* Obecné entity – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/general`

[Verze rozpoznávání pojmenovaných entit verze 3,1-Preview pro `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral)

* Osobní ( `PII` ) informace – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii`

`domain=phi`K detekci `PHI` informací o stavu () v textu můžete použít také volitelný parametr. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii?domain=phi`

Všimněte si, že přidání `redactedText` vlastnosti do odpovědi JSON, která obsahuje upravený vstupní text, kde jsou zjištěné entity PII nahrazeny znakem * pro každý znak entit.

[Verze rozpoznávání pojmenovaných entit verze 3,1-Preview pro `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii)

---

Nastavte hlavičku požadavku tak, aby obsahovala klíč rozhraní API pro analýzu textu. V textu žádosti zadejte dokumenty JSON, které jste připravili.

### <a name="example-ner-request"></a>Příklad žádosti NER 

Následuje příklad obsahu, který můžete odeslat do rozhraní API. Formát požadavku je pro obě verze rozhraní API stejný.

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

## <a name="post-the-request"></a>Publikování žádosti

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavků, které můžete odeslat za minutu a sekundy, najdete v části [omezení dat](../overview.md#data-limits) v přehledu.

Rozhraní API pro analýzu textu je Bezstavová. Ve vašem účtu se neukládají žádná data a výsledky se vrátí hned v odpovědi.

## <a name="view-results"></a>Zobrazení výsledků

Všechny žádosti POST vrátí odpověď ve formátu JSON s ID a zjištěnými vlastnostmi entity.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo můžete výstup uložit do souboru v místním systému a potom ho naimportovat do aplikace, která umožňuje řadit a vyhledávat data a pracovat s nimi. Vzhledem k podpoře vícejazyčných a Emoji může odpověď obsahovat posunutí textu. Další informace najdete v tématu [postup zpracování posunutí textu](../concepts/text-offsets.md) .

### <a name="example-responses"></a>Příklady odpovědí

Verze 3 poskytuje samostatné koncové body pro NER a propojení entit. Odpovědi pro obě operace jsou uvedené níže. 

#### <a name="version-30"></a>[Verze 3,0](#tab/version-3)

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
#### <a name="version-31-preview"></a>[Verze 3,1-Preview](#tab/version-3-preview)

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

---


## <a name="summary"></a>Shrnutí

V tomto článku jste zjistili koncepty a pracovní postupy pro propojení entit pomocí Analýza textu v Cognitive Services. Souhrn:

* Dokumenty JSON v textu požadavku zahrnují ID, text a kód jazyka.
* Žádosti POST se odesílají do jednoho nebo více koncových bodů s použitím přizpůsobeného [přístupového klíče a koncového bodu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , který je platný pro vaše předplatné.
* Výstup odpovědi, který se skládá z propojených entit (včetně hodnocení spolehlivosti, posunu a webových odkazů, pro každé ID dokumentu), se dá použít v libovolné aplikaci.

## <a name="next-steps"></a>Další kroky

* [Přehled analýzy textu](../overview.md)
* [Použití klientské knihovny Analýza textu](../quickstarts/text-analytics-sdk.md)
* [Co je nového](../whats-new.md)
