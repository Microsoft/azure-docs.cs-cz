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
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 457be5ac014fda6b4984ed7af3dcc89780b16379
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2020
ms.locfileid: "84141613"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Jak používat rozpoznávání pojmenovaných entit v Analýza textu

Rozhraní API pro analýzu textu umožňuje přebírá nestrukturovaný text a vrací seznam nejednoznačných entit s odkazy na Další informace na webu. Rozhraní API podporuje rozpoznávání pojmenovaných entit (NER) i propojení entit.

### <a name="entity-linking"></a>Entity Linking

Propojení entit je schopnost identifikovat a odstranit identitu entity nalezenou v textu (například určit, zda výskyt slova "Mars" odkazuje na globálním nebo římské jsou války). Tento proces vyžaduje přítomnost znalostní báze v příslušném jazyce, aby bylo možné propojit rozpoznané entity v textu. Při propojování entit se jako tato znalostní báze používá [Wikipedii](https://www.wikipedia.org/) .


### <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

Rozpoznávání pojmenovaných entit (NER) je schopnost identifikovat různé entity v textu a kategorizovat je do předem definovaných tříd nebo typů, jako je například osoba, umístění, událost, produkt a organizace.  

## <a name="named-entity-recognition-versions-and-features"></a>Verze a funkce pro rozpoznávání pojmenovaných entit

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Příznak                                                         | NER v 3.0 | NER verze 3.1 – Preview. 1 |
|-----------------------------------------------------------------|--------|----------|
| Metody pro jednotlivé a dávkové požadavky                          | X      | X        |
| Rozšířené rozpoznávání entit napříč několika kategoriemi           | X      | X        |
| Samostatné koncové body pro posílání NER entit a žádostí o připojení. | X      | X        |
| Rozpoznávání osobních entit ( `PII` ) a `PHI` informací o stavu ()        |        | X        |

Informace najdete v tématu [Podpora jazyků](../language-support.md) .

### <a name="entity-types"></a>Typy entit

Rozpoznávání pojmenovaných entit V3 poskytuje rozšířené zjišťování napříč více typy. V současné době NER v 3.0 dokáže rozpoznat entity v [kategorii obecné entity](../named-entity-types.md).

Rozpoznávání pojmenovaných entit v 3.1-Preview. 1 zahrnuje možnosti detekce v 3.0 a možnost detekovat osobní údaje ( `PII` ) pomocí `v3.1-preview.1/entities/recognition/pii` koncového bodu. `domain=phi`K detekci důvěrných informací o stavu () můžete použít volitelný parametr `PHI` . Další informace najdete níže v části věnované předmětům [kategorie](../named-entity-types.md) a [koncovým bodům žádosti](#request-endpoints) .


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

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

#### <a name="version-31-preview1"></a>[Verze 3,1-Preview. 1](#tab/version-3-preview)

Rozpoznávání pojmenovaných entit `v3.1-preview.1` používá samostatné koncové body pro žádosti ner a propojení entit. V závislosti na vaší žádosti použijte formát adresy URL:

Propojení entit
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/linking`

NER
* Obecné entity –`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/general`

* Osobní ( `PII` ) informace –`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii`

`domain=phi`K detekci `PHI` informací o stavu () v textu můžete použít také volitelný parametr. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi`

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

### <a name="example-v3-responses"></a>Příklad odpovědí V3

Verze 3 poskytuje samostatné koncové body pro NER a propojení entit. Odpovědi pro obě operace jsou uvedené níže. 

#### <a name="example-ner-response"></a>Příklad odpovědi NER

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


#### <a name="example-entity-linking-response"></a>Příklad odpovědi propojení entit

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
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


## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postupy pro propojení entit pomocí Analýza textu v Cognitive Services. Souhrn:

* Dokumenty JSON v textu požadavku zahrnují ID, text a kód jazyka.
* Žádosti POST se odesílají do jednoho nebo více koncových bodů s použitím přizpůsobeného [přístupového klíče a koncového bodu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , který je platný pro vaše předplatné.
* Výstup odpovědi, který se skládá z propojených entit (včetně hodnocení spolehlivosti, posunu a webových odkazů, pro každé ID dokumentu), se dá použít v libovolné aplikaci.

## <a name="next-steps"></a>Další kroky

* [Přehled Analýza textu](../overview.md)
* [Použití klientské knihovny Analýza textu](../quickstarts/text-analytics-sdk.md)
* [Co je nového](../whats-new.md)
