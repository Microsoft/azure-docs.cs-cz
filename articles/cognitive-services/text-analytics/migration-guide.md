---
title: Průvodce migrací pro v2 rozhraní API pro analýzu textu
titleSuffix: Azure Cognitive Services
description: Naučte se, jak přesunout své aplikace, aby používaly verzi 3 rozhraní API pro analýzu textu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/22/2021
ms.author: aahi
ms.openlocfilehash: 416ef4ceddbb43e9f1606d44a66ffd5295cee4e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "101699891"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Migrace na verzi 3. x z rozhraní API pro analýzu textu

Pokud používáte verzi 2,1 rozhraní API pro analýzu textu, Tento článek vám pomůže při upgradu aplikace na použití verze 3. x. Verze 3,0 je všeobecně dostupná a přináší nové funkce, jako jsou rozšířené [rozpoznávání pojmenovaných entit (ner)](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-features-and-versions) a [Správa verzí modelů](concepts/model-versioning.md). K dispozici je také verze Preview verze 3.1 (v 3.1 – Preview. x), která přidává funkce, jako je třeba dolování v podobě [názoru](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features). Modely používané v v2 nebudou dostávat budoucí aktualizace. 

## <a name="sentiment-analysis"></a>[Analýza mínění](#tab/sentiment-analysis)

### <a name="feature-changes"></a>Změny funkcí 

Analýza mínění ve verzi 2,1 vrátí mínění skóre mezi 0 a 1 pro každý dokument odeslaný do rozhraní API, přičemž skóre se blíží hodnotě 1, která značí kladné mínění. Verze 3 místo toho vrátí popisky mínění (například "kladné" nebo "negativní") pro obě věty i dokument jako celek a jejich související výsledky. 

### <a name="steps-to-migrate"></a>Postup migrace

#### <a name="rest-api"></a>REST API

Pokud vaše aplikace používá REST API, aktualizujte koncový bod požadavku na koncový bod v3 pro analýzu mínění. Například: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment` . Také budete muset aplikaci aktualizovat tak, aby používala popisky mínění vrácené v [odpovědi rozhraní API](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results). 

Příklady odpovědi JSON najdete v referenční dokumentaci.
* [Verze 2,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [Verze 3,0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [Verze 3,1-Preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="client-libraries"></a>Klientské knihovny

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="ner-and-entity-linking"></a>[NER a propojení entit](#tab/named-entity-recognition)

### <a name="feature-changes"></a>Změny funkcí

Ve verzi 2,1 používá rozhraní API pro analýzu textu jeden koncový bod pro rozpoznávání pojmenovaných entit (NER) a propojení entit. Verze 3 poskytuje rozšířenou detekci pojmenovaných entit a používá samostatné koncové body pro žádosti NER a propojení entit. Počínaje verzí v 3.1 – Preview. 1 může NER dále detekovat osobní `pii` `phi` údaje a informace o stavu. 

### <a name="steps-to-migrate"></a>Postup migrace

#### <a name="rest-api"></a>REST API

Pokud vaše aplikace používá REST API, aktualizujte její koncový bod požadavku na koncové body v3 pro propojení NER a/nebo entit.

Entity Linking
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

Také budete muset aplikaci aktualizovat tak, aby používala [kategorie entit](named-entity-types.md) vrácené v [odpovědi rozhraní API](how-tos/text-analytics-how-to-entity-linking.md#view-results).

Příklady odpovědi JSON najdete v referenční dokumentaci.
* [Verze 2,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [Verze 3,0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [Verze 3,1-Preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/EntitiesRecognitionGeneral)

#### <a name="client-libraries"></a>Klientské knihovny

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="version-21-entity-categories"></a>Kategorie entit verze 2,1

V následující tabulce jsou uvedeny kategorie entit vrácené pro NER v 2.1.

| Kategorie   | Popis                          |
|------------|--------------------------------------|
| Person (Osoba)   |   Jména lidí.  |
|Umístění    | Přírodní a lidský orientačních bodů, struktur, geografických funkcí a geopolitických entit |
|Organizace | Společnosti, politické skupiny, hudební pásma, sportovní klub, státní orgány a veřejné organizace. Státní příslušníky a náboženství nejsou zahrnuté do tohoto typu entity. |
| PhoneNumber | Telefonní čísla (jenom USA a telefonní čísla EU). |
| E-mail | E-mailové adresy. |
| URL | Adresy URL webů. |
| IP adresa | Síťové IP adresy. |
| DateTime | Data a denní doba.| 
| Date (Datum) | Kalendářní data kalendáře |
| Čas | Denní doba |
| Rozsah dat | Rozsahy dat. |
| Časový rozsah | Časové rozsahy. |
| Doba trvání | Dob trvání. |
| Nastavit | Nastavte opakující se časy. |
| Množství | Čísla a číselná množství. |
| Číslo | Hodnoty. |
| Procento | Procenta.|
| Řádová číslovka | Pořadová čísla |
| Věk | Ve věku. |
| Měna | Měnami. |
| Dimenze | Rozměry a měření. |
| Teplota | Teplot. |

## <a name="language-detection"></a>[Rozpoznávání jazyka](#tab/language-detection)

### <a name="feature-changes"></a>Změny funkcí 

Výstup funkce pro detekci jazyka se změnil v v3. Odpověď JSON bude obsahovat `ConfidenceScore` místo `score` . V3 také vrátí pouze jeden jazyk v  `detectedLanguage` atributu pro každý dokument.

### <a name="steps-to-migrate"></a>Postup migrace

#### <a name="rest-api"></a>REST API

Pokud vaše aplikace používá REST API, aktualizujte koncový bod požadavku na koncový bod v3 pro rozpoznání jazyka. Například: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages` . Také budete muset aplikaci aktualizovat, aby se `ConfidenceScore` místo `score` v [odpovědi rozhraní API](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results)používala. 

Příklady odpovědi JSON najdete v referenční dokumentaci.
* [Verze 2,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [Verze 3,0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [Verze 3,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

#### <a name="client-libraries"></a>Klientské knihovny

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="key-phrase-extraction"></a>[Extrakce klíčových frází](#tab/key-phrase-extraction)

### <a name="feature-changes"></a>Změny funkcí 

Funkce Extrakce klíčových frází se v systému V3 nezměnila mimo verzi koncového bodu.

### <a name="steps-to-migrate"></a>Postup migrace

#### <a name="rest-api"></a>REST API

Pokud vaše aplikace používá REST API, aktualizujte koncový bod požadavku na koncový bod v3 pro extrakci klíčových frází. Příklad: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

Příklady odpovědi JSON najdete v referenční dokumentaci.
* [Verze 2,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [Verze 3,0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [Verze 3,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/KeyPhrases)

#### <a name="client-libraries"></a>Klientské knihovny

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---

## <a name="see-also"></a>Viz také

* [Co je rozhraní API pro analýzu textu](overview.md)
* [Podpora jazyků](language-support.md)
* [Správa verzí modelů](concepts/model-versioning.md)
