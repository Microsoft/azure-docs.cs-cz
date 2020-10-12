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
ms.date: 06/25/2020
ms.author: aahi
ms.openlocfilehash: 12c09ad8e1db3914263fcc864c9c2d09069d63a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85412579"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Migrace na verzi 3. x z rozhraní API pro analýzu textu

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

Pokud používáte verzi 2,1 rozhraní API pro analýzu textu, Tento článek vám pomůže při upgradu aplikace na použití verze 3. x. Verze 3,0 je všeobecně dostupná a přináší nové funkce, jako jsou rozšířené [rozpoznávání pojmenovaných entit (ner)](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) a [Správa verzí modelů](concepts/model-versioning.md). K dispozici je také verze Preview verze 3.1 (v 3.1 – Preview. x), která přidává funkce, jako je třeba dolování v podobě [názoru](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features). Modely používané v v2 nebudou dostávat budoucí aktualizace. 

#### <a name="sentiment-analysis"></a>[Analýza mínění](#tab/sentiment-analysis)

## <a name="feature-changes"></a>Změny funkcí 

Analýza mínění ve verzi 2,1 vrátí mínění skóre mezi 0 a 1 pro každý dokument odeslaný do rozhraní API, přičemž skóre se blíží hodnotě 1, která značí kladné mínění. Verze 3 místo toho vrátí popisky mínění (například "kladné" nebo "negativní") pro obě věty i dokument jako celek a jejich související výsledky. 

## <a name="steps-to-migrate"></a>Postup migrace

### <a name="rest-api"></a>REST API

Pokud vaše aplikace používá REST API, aktualizujte koncový bod požadavku na koncový bod v3 pro analýzu mínění. Například: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment` . Také budete muset aplikaci aktualizovat tak, aby používala popisky mínění vrácené v [odpovědi JSON](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results). 

### <a name="client-libraries"></a>Klientské knihovny

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="ner-and-entity-linking"></a>[NER a propojení entit](#tab/named-entity-recognition)

## <a name="feature-changes"></a>Změny funkcí

> [!NOTE] 
> V současné době se [kategorie entity V3](named-entity-types.md) vrátí jenom pro anglický a španělský text. Rozhraní API vrátí výsledky verze 2,1 pro žádosti v jiných jazycích za předpokladu, že jsou podporované ve verzi 2,1.

Ve verzi 2,1 používá rozhraní API pro analýzu textu jeden koncový bod pro rozpoznávání pojmenovaných entit (NER) a propojení entit. Verze 3 poskytuje rozšířenou detekci pojmenovaných entit a používá samostatné koncové body pro žádosti NER a propojení entit. Počínaje verzí v 3.1 – Preview. 1 může NER dále detekovat osobní `pii` `phi` údaje a informace o stavu. 

## <a name="steps-to-migrate"></a>Postup migrace

### <a name="rest-api"></a>REST API

Pokud vaše aplikace používá REST API, aktualizujte její koncový bod požadavku na koncové body v3 pro propojení NER a/nebo entit.

Entity Linking
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

Také budete muset aplikaci aktualizovat tak, aby používala [kategorie entit](named-entity-types.md) vrácené v [odpovědi JSON](how-tos/text-analytics-how-to-entity-linking.md#view-results).

### <a name="client-libraries"></a>Klientské knihovny

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="language-detection"></a>[Rozpoznávání jazyka](#tab/language-detection)

## <a name="feature-changes"></a>Změny funkcí 

Funkce detekce jazyka se v systému V3 nezměnila mimo verzi koncového bodu, ale odpověď JSON bude obsahovat `ConfidenceScore` místo `score` . V3 také vrátí pouze jeden jazyk ve výstupu. 

## <a name="steps-to-migrate"></a>Postup migrace

### <a name="rest-api"></a>REST API

Pokud vaše aplikace používá REST API, aktualizujte koncový bod požadavku na koncový bod v3 pro rozpoznání jazyka. Například: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages` . Také budete muset aplikaci aktualizovat, aby používala `ConfidenceScore` místo `score` v [odpovědi JSON](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results). 

### <a name="client-libraries"></a>Klientské knihovny

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="key-phrase-extraction"></a>[Extrakce klíčových frází](#tab/key-phrase-extraction)

## <a name="feature-changes"></a>Změny funkcí 

Funkce Extrakce klíčových frází se v systému V3 nezměnila mimo verzi koncového bodu.

## <a name="steps-to-migrate"></a>Postup migrace

### <a name="rest-api"></a>REST API

Pokud vaše aplikace používá REST API, aktualizujte koncový bod požadavku na koncový bod v3 pro extrakci klíčových frází. Příklad: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

### <a name="client-libraries"></a>Klientské knihovny

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---


## <a name="see-also"></a>Viz také

* [Reference k rozhraní API pro analýzu textu v2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/)
* [Co je rozhraní API pro analýzu textu](overview.md)
* [Podpora jazyků](language-support.md)
* [Správa verzí modelů](concepts/model-versioning.md)