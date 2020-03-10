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
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 0622aca5579c64c6d840761abb151665af559eea
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942658"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Jak používat rozpoznávání pojmenovaných entit v Analýza textu

Rozhraní API pro analýzu textu umožňuje přebírá nestrukturovaný text a vrací seznam nejednoznačných entit s odkazy na Další informace na webu. Rozhraní API podporuje rozpoznávání pojmenovaných entit (NER) i propojení entit.

### <a name="entity-linking"></a>Entity Linking

Propojení entit je schopnost identifikovat a odstranit identitu entity nalezenou v textu (například určit, zda výskyt slova `Mars` odkazuje na globálním nebo římské jsou války). Tento proces vyžaduje přítomnost znalostní báze v příslušném jazyce, aby bylo možné propojit rozpoznané entity v textu. Při propojování entit se jako tato znalostní báze používá [Wikipedii](https://www.wikipedia.org/) .


### <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

Rozpoznávání pojmenovaných entit (NER) je schopnost identifikovat různé entity v textu a kategorizovat je do předem definovaných tříd nebo typů, jako je například osoba, umístění, událost, produkt a organizace.  

Od verze 3 může tato funkce rozhraní API pro analýzu textu také identifikovat osobní a citlivé typy informací, jako je telefonní číslo, číslo sociálního pojištění, e-mailová adresa a číslo bankovního účtu.  Identifikace těchto entit může pomoci při klasifikaci citlivých dokumentů a redigování osobních údajů.

## <a name="named-entity-recognition-versions-and-features"></a>Verze a funkce pro rozpoznávání pojmenovaných entit

Rozhraní API pro analýzu textu nabízí dvě verze rozpoznávání pojmenovaných entit – v2 a v3. Verze 3 (Public Preview) poskytuje zvýšené podrobnosti o entitách, které se dají detekovat a kategorizovat.

| Funkce                                                         | NER v2 | NER V3 |
|-----------------------------------------------------------------|--------|--------|
| Metody pro jednotlivé a dávkové požadavky                          | X      | X      |
| Základní rozpoznávání entit v několika kategoriích              | X      | X      |
| Rozšířená klasifikace pro rozpoznané entity                 |        | X      |
| Samostatné koncové body pro posílání NER entit a žádostí o připojení. |        | X      |
| Správa verzí modelů                                                |        | X      |

Informace najdete v tématu [Podpora jazyků](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) .


#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

### <a name="entity-types"></a>Typy entit

Rozpoznávání pojmenovaných entit V3 poskytuje rozšířené zjišťování napříč více typy. NER V3 v současné době dokáže rozpoznat následující kategorie entit:

* Obecné
* Osobní údaje 

Podrobný seznam podporovaných entit a jazyků najdete v článku [podporované typy entit ner V3](../named-entity-types.md) .

### <a name="request-endpoints"></a>Koncové body požadavku

Rozpoznávání pojmenovaných entit V3 používá samostatné koncové body pro žádosti NER a propojení entit. V závislosti na vaší žádosti použijte formát adresy URL:

NER
* Obecné entity – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Osobní informace – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Propojení entit
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Správa verzí modelů

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

### <a name="entity-types"></a>Typy entit

> [!NOTE]
> Rozpoznávání pojmenovaných entit (NER) verze 2 podporuje pouze následující entity. NER v3 je ve verzi Public Preview a významně rozšiřuje počet a hloubku entit rozpoznaných v textu.   

| Typ  | SubType | Příklad |
|:-----------   |:------------- |:---------|
| Osoba        | Není k dispozici\*         | Jan, vyúčtování Branch     |
| Umístění      | Není k dispozici\*         | "Redmond, Washington", "Paříž"  |
| Organizace  | Není k dispozici\*         | Microsoft   |
| Množství      | Počet        | "6", "šest"     |
| Množství      | Procento    | "50%", "50 procent"|
| Množství      | Řadový       | "2.", "Second"     |
| Množství      | Stáří           | "90 den starý", "30 let starý"    |
| Množství      | Měna      | "$10,99"     |
| Množství      | Dimenze     | "10 mil", "40 cm"     |
| Množství      | Temperature   | "32 stupňů"    |
| Datum a čas      | Není k dispozici\*         | "6:17:30 4. února 2012"      |
| Datum a čas      | Datum          | "Květen 2, 2017", "05/02/2017"   |
| Datum a čas      | Čas          | "8:00", "8:00"  |
| Datum a čas      | DateRange     | "Může 2. května až 5."    |
| Datum a čas      | TimeRange     | "18:00 to 19:00"     |
| Datum a čas      | Doba platnosti      | 1 minuta a 45 sekund   |
| Datum a čas      | Nastavit           | "každé úterý"     |
| URL           | Není k dispozici\*         | "https:\//www.bing.com"    |
| E-mail         | Není k dispozici\*         | support@contoso.com |
| Telefonní číslo v USA  | Není k dispozici\*         | (Jenom telefonní čísla USA) "(312) 555-0176" |
| IP adresa    | Není k dispozici\*         | 10.0.0.100 |

\* v závislosti na vstupních a extrahovaných entitách, mohou některé entity vynechat `SubType`.  Všechny uvedené podporované typy entit jsou k dispozici pouze pro jazyky anglické, zjednodušené, francouzštiny, němčiny a španělštiny.

### <a name="request-endpoints"></a>Koncové body požadavku

Rozpoznávání pojmenovaných entit v2 používá jeden koncový bod pro požadavky NER a propojení entit:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>Odesílá se žádost o REST API.

### <a name="preparation"></a>Příprava

Je nutné mít dokumenty JSON v tomto formátu: ID, text, jazyk.

Každý dokument musí být pod 5 120 znaky a pro každou kolekci můžete mít až 1 000 položek (ID). Kolekce se posílá v textu žádosti.

### <a name="structure-the-request"></a>Strukturování žádosti

Vytvoření žádosti POST. Můžete [použít post](text-analytics-how-to-call-api.md) nebo **konzolu testování API** v následujících odkazech k rychlému uspořádání a odeslání jednoho. 

> [!NOTE]
> Klíč a koncový bod pro váš Analýza textu prostředek najdete na webu Azure Portal. Budou se nacházet na stránce **rychlý Start** prostředku v části **Správa prostředků**. 

#### <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

[Referenční informace o rerozpoznávání entity V3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

Verze 3 používá samostatné koncové body pro žádosti NER a propojení entit. V závislosti na vaší žádosti použijte formát adresy URL:

NER
* Obecné entity – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Entity osobních informací – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Propojení entit
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

[Referenční informace o rozpoznávání pojmenovaných entit (NER) v2](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

Verze 2 používá pro požadavky entity propojování a NER následující koncový bod: 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

Nastavte hlavičku požadavku tak, aby obsahovala klíč rozhraní API pro analýzu textu. V textu žádosti zadejte dokumenty JSON, které jste připravili.

### <a name="example-ner-request"></a>Příklad žádosti NER 

Následuje příklad obsahu, který můžete odeslat do rozhraní API. Formát požadavku je pro obě verze rozhraní API stejný.

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "I had a wonderful trip to Seattle last week."
    }
  ]
}
```

## <a name="post-the-request"></a>Publikování žádosti

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavků, které můžete odeslat za minutu a sekundy, najdete v části [omezení dat](../overview.md#data-limits) v přehledu.

Rozhraní API pro analýzu textu je Bezstavová. Ve vašem účtu se neukládají žádná data a výsledky se vrátí hned v odpovědi.

## <a name="view-results"></a>Zobrazení výsledků

Všechny žádosti POST vrátí odpověď ve formátu JSON s ID a zjištěnými vlastnostmi entity.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo můžete výstup uložit do souboru v místním systému a potom ho naimportovat do aplikace, která umožňuje řadit a vyhledávat data a pracovat s nimi.


#### <a name="version-30-preview"></a>[Verze 3,0-Preview)](#tab/version-3)

### <a name="example-v3-responses"></a>Příklad odpovědí V3

Verze 3 poskytuje samostatné koncové body pro NER a propojení entit. Odpovědi pro obě operace jsou uvedené níže.

#### <a name="example-ner-response"></a>Příklad odpovědi NER

```json
{
    "documents": [{
    "id": "1",
    "entities": [{
        "text": "Seattle",
        "type": "Location",
        "offset": 26,
        "length": 7,
        "score": 0.80624294281005859
    }, {
        "text": "last week",
        "type": "DateTime",
        "subtype": "DateRange",
        "offset": 34,
        "length": 9,
        "score": 0.8
    }]
    }],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

#### <a name="example-entity-linking-response"></a>Příklad odpovědi propojení entit

```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "text": "Seattle",
        "offset": 26,
        "length": 7,
        "score": 0.15046201222847677
      }],
      "language": "en",
      "id": "Seattle",
      "url": "https://en.wikipedia.org/wiki/Seattle",
      "dataSource": "Wikipedia"
    }]
  }],
  "errors": [],
  "modelVersion": "2019-10-01"
}
```

#### <a name="version-21"></a>[Verze 2,1](#tab/version-2)

### <a name="example-ner-v2-response"></a>Příklad odpovědi NER v2
```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "wikipediaScore": 0.15046201222847677,
        "entityTypeScore": 0.80624294281005859,
        "text": "Seattle",
        "offset": 26,
        "length": 7
      }],
      "wikipediaLanguage": "en",
      "wikipediaId": "Seattle",
      "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle",
      "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
      "type": "Location"
    }, {
      "name": "last week",
      "matches": [{
        "entityTypeScore": 0.8,
        "text": "last week",
        "offset": 34,
        "length": 9
      }],
      "type": "DateTime",
      "subType": "DateRange"
    }]
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postupy pro propojení entit pomocí Analýza textu v Cognitive Services. Souhrn:

* Rozpoznávání pojmenovaných entit je k dispozici pro vybrané jazyky ve dvou verzích.
* Dokumenty JSON v textu požadavku zahrnují ID, text a kód jazyka.
* Žádosti POST se odesílají do jednoho nebo více koncových bodů s použitím přizpůsobeného [přístupového klíče a koncového bodu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , který je platný pro vaše předplatné.
* Výstup odpovědi, který se skládá z propojených entit (včetně hodnocení spolehlivosti, posunu a webových odkazů, pro každé ID dokumentu), se dá použít v libovolné aplikaci.

## <a name="next-steps"></a>Další kroky

* [Přehled rozhraní API pro analýzu textu](../overview.md)
* [Použití klientské knihovny Analýza textu](../quickstarts/text-analytics-sdk.md)
* [Co je nového](../whats-new.md)
