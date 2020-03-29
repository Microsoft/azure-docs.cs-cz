---
title: Použití rozpoznávání entit s rozhraním API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak identifikovat a rozvést identitu entity nalezené v textu pomocí rozhraní REST API pro analýzu textu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 243086ddaae47eba20eea6877fe6d7f8f9889290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79203487"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Použití rozpoznávání pojmenovaných entit v textové analýze

Rozhraní API pro analýzu textu umožňuje pořizovat nestrukturovaný text a vrátí seznam nerozdělených entit s odkazy na další informace na webu. Rozhraní API podporuje rozpoznávání pojmenovaných entit (NER) i propojení entit.

### <a name="entity-linking"></a>Propojení entit

Entity propojení je schopnost identifikovat a rozptýlit identitu entity nalezené v textu (například určení, zda `Mars` výskyt slova odkazuje na planetu, nebo na římského boha války). Tento proces vyžaduje přítomnost znalostní báze v příslušném jazyce, aby bylo k propojení uznaných entit v textu propojeno. Entity Linking používá [Wikipedii](https://www.wikipedia.org/) jako tuto znalostní bázi.


### <a name="named-entity-recognition-ner"></a>Uznání pojmenované entity (NER)

Rozpoznávání pojmenovaných entit (NER) je schopnost identifikovat různé entity v textu a kategorizovat je do předdefinovaných tříd nebo typů, jako jsou: osoba, umístění, událost, produkt a organizace.  

Počínaje verzí 3 může tato funkce rozhraní API pro analýzu textu také identifikovat typy osobních a citlivých informací, jako jsou: telefonní číslo, číslo sociálního pojištění, e-mailová adresa a číslo bankovního účtu.  Identifikace těchto entit může pomoci při klasifikaci citlivých dokumentů a redigování osobních údajů.

## <a name="named-entity-recognition-versions-and-features"></a>Verze a funkce rozpoznávání pojmenovaných entit

Rozhraní API pro analýzu textu nabízí dvě verze rozpoznávání pojmenovaných entit – v2 a v3. Verze 3 (Public Preview) poskytuje větší podrobnosti v entitách, které lze zjistit a kategorizovat.

| Funkce                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| Metody pro jednoduché a dávkové požadavky                          | ×      | ×      |
| Uznání základní entity v několika kategoriích              | ×      | ×      |
| Rozšířená klasifikace pro uznané entity                 |        | ×      |
| Samostatné koncové body pro odesílání propojení entit a požadavků NER. |        | ×      |
| Správa verzí modelu                                                |        | ×      |

Informace naleznete v [jazykové podpoře.](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition)


#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

### <a name="entity-types"></a>Typy entit

Pojmenovaný rozpoznávání entit v3 poskytuje rozšířené zjišťování napříč více typy. V současné době může ner v3 rozpoznat následující kategorie entit:

* Obecné
* Osobní údaje 

Podrobný seznam podporovaných entit a jazyků naleznete v článku [typy podporovaných entit NER v3.](../named-entity-types.md)

### <a name="request-endpoints"></a>Požadavek na koncové body

Pojmenovaný rozpoznávání entit v3 používá samostatné koncové body pro NER a požadavky na propojení entit. Použijte níže uvedený formát adresy URL na základě vašeho požadavku:

Ner
* Obecné subjekty -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Osobní údaje -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Propojení entit
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Správa verzí modelu

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

### <a name="entity-types"></a>Typy entit

> [!NOTE]
> Pojmenovaný rozpoznávání entit (NER) verze 2 podporuje pouze následující entity. NER v3 je ve verzi Public Preview a výrazně rozšiřuje počet a hloubku entit rozpoznaných v textu.   

| Typ  | Podtypu | Příklad |
|:-----------   |:------------- |:---------|
| Person (Osoba)        | N/a\*         | "Jeff", "Bill Gates"     |
| Umístění      | N/a\*         | "Redmond, Washington", "Paříž"  |
| Organizace  | N/a\*         | "Microsoft"   |
| Množství      | Číslo        | "6", "šest"     |
| Množství      | Procento    | "50%", "padesát procent"|
| Množství      | Pořadové       | "2.", "druhý"     |
| Množství      | Věk           | "90 dní starý", "30 let"    |
| Množství      | Měna      | "$10.99"     |
| Množství      | Dimenze     | "10 mil", "40 cm"     |
| Množství      | Teplota   | "32 stupňů"    |
| DateTime      | N/a\*         | "18:30 4.února 2012"      |
| DateTime      | Datum          | "2. května 2017", "05.02.2017"   |
| DateTime      | Time          | "8:00", "8:00"  |
| DateTime      | Rozsah dat     | "2. května až 5. května"    |
| DateTime      | Časový rozsah     | "18:00 až 19:00"     |
| DateTime      | Doba trvání      | "1 minuta a 45 sekund"   |
| DateTime      | Nastavit           | "každé úterý"     |
| zprostředkovatele identity           | N/a\*         | "https:\//www.bing.com"    |
| E-mail         | N/a\*         | "support@contoso.com" |
| Americké telefonní číslo  | N/a\*         | (pouze telefonní čísla v USA) "(312) 555-0176" |
| IP adresa    | N/a\*         | "10.0.0.100" |

\*V závislosti na vstupních a extrahovaných entitách `SubType`mohou určité entity vynechat soubor .  Všechny uvedené typy podporovaných entit jsou k dispozici pouze pro angličtinu, čínštinu zjednodušenou, francouzštinu, němčinu a španělštinu.

### <a name="request-endpoints"></a>Požadavek na koncové body

Pojmenovaný rozpoznávání entit v2 používá jeden koncový bod pro NER a požadavky na propojení entit:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>Odeslání požadavku rozhraní REST API

### <a name="preparation"></a>Příprava

Musíte mít dokumenty JSON v tomto formátu: ID, text, jazyk.

Každý dokument musí mít méně než 5 120 znaků a můžete mít až 1 000 položek (ID) na kolekci. Kolekce se posílá v textu žádosti.

### <a name="structure-the-request"></a>Strukturujte požadavek

Vytvořte žádost POST. Můžete [použít Postman](text-analytics-how-to-call-api.md) nebo **testovací konzole rozhraní API** v následujících odkazech rychle strukturovat a odeslat jeden. 

> [!NOTE]
> Klíč a koncový bod pro prostředek Analýzy textu najdete na webu Azure Portal. Budou umístěny na stránce **rychlého startu** zdroje v části **Správa zdrojů**. 

#### <a name="version-30-preview"></a>[Verze 3.0-preview](#tab/version-3)

[Odkaz na uznané rozpoznávání entit v3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

Verze 3 používá samostatné koncové body pro ner a entity propojení požadavků. Použijte níže uvedený formát adresy URL na základě vašeho požadavku:

Ner
* Obecné subjekty -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Subjekty s osobními údaji -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Propojení entit
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

[Odkaz na uznání pojmenované entity (NER) v2](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

Verze 2 používá následující koncový bod pro propojení entit a požadavky programu NER: 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

Nastavte záhlaví požadavku tak, aby obsahovalo klíč rozhraní API pro analýzu textu. V textu požadavku zadejte dokumenty JSON, které jste připravili.

### <a name="example-ner-request"></a>Příklad požadavku NER 

Následuje příklad obsahu, který můžete odeslat do rozhraní API. Formát požadavku je stejný pro obě verze rozhraní API.

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

## <a name="post-the-request"></a>Zaúčtovat žádost

Analýza se provede po přijetí žádosti. Informace o velikosti a počtu požadavků, které můžete odeslat za minutu a sekundu, naleznete v části [Datové limity](../overview.md#data-limits) v přehledu.

Rozhraní API analýzy textu je bezstavové. Ve vašem účtu nejsou uložena žádná data a výsledky jsou okamžitě vráceny v odpovědi.

## <a name="view-results"></a>Zobrazení výsledků

Všechny požadavky POST vrátí odpověď ve formátu JSON s ID a zjištěnými vlastnostmi entity.

Výstup se vrátí okamžitě. Výsledky můžete streamovat do aplikace, která přijímá JSON, nebo můžete výstup uložit do souboru v místním systému a potom ho naimportovat do aplikace, která umožňuje řadit a vyhledávat data a pracovat s nimi. Vzhledem k podpoře vícejazyčných a emoji může odpověď obsahovat posuny textu. Další informace naleznete [v tématu Zpracování posunů textu.](../concepts/text-offsets.md)

#### <a name="version-30-preview"></a>[Verze 3.0-preview)](#tab/version-3)

### <a name="example-v3-responses"></a>Příklad odpovědí v3

Verze 3 poskytuje samostatné koncové body pro ner a propojení entit. Odpovědi pro obě operace jsou níže. 

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

#### <a name="example-entity-linking-response"></a>Příklad odpovědi na propojení entit

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

#### <a name="version-21"></a>[Verze 2.1](#tab/version-2)

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

V tomto článku jste se dozvěděli koncepty a pracovní postupy pro propojení entit pomocí analýzy textu v kognitivních službách. Souhrn:

* Rozpoznávání pojmenovaných entit je k dispozici pro vybrané jazyky ve dvou verzích.
* Dokumenty JSON v textu požadavku obsahují ID, text a kód jazyka.
* Požadavky POST se posílají do jednoho nebo více koncových bodů pomocí přizpůsobeného [přístupového klíče a koncového bodu,](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) který je platný pro vaše předplatné.
* Výstup odpovědi, který se skládá z propojených entit (včetně skóre spolehlivosti, posunů a webových odkazů pro každé ID dokumentu) lze použít v libovolné aplikaci

## <a name="next-steps"></a>Další kroky

* [Přehled analýzy textu](../overview.md)
* [Použití klientské knihovny Analýzy textu](../quickstarts/text-analytics-sdk.md)
* [Co je nového](../whats-new.md)
