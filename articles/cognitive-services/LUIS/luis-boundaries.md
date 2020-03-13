---
title: Omezení – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje známé omezení z Azure Cognitive Services LUIS (Language Understanding). Služba LUIS má několik oblasti hranic. Model hranic řídí záměrů, entit a funkcí v LUIS. Limity kvót podle typu klíče. Kombinace kláves řídí LUIS webu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: d584b00caef628eb9dfd085b1fdce2bb7b353988
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220080"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Hranice pro LUIS model a klíče
Služba LUIS má několik oblasti hranic. První je [hranice modelu](#model-boundaries), která řídí záměry, entity a funkce v Luis. Druhá oblast má [omezení kvóty](#key-limits) na základě typu klíče. Třetí oblastí hranice je [kombinace kláves](#keyboard-controls) pro řízení webu Luis. Čtvrtá oblast je [mapování světové oblasti](luis-reference-regions.md) mezi vytvářením webu Luis a rozhraními API [koncového bodu](luis-glossary.md#endpoint) Luis.


## <a name="model-boundaries"></a>Model hranice

Pokud vaše aplikace překračuje limity a hranice modelu LUIS, zvažte použití aplikace [Luis Dispatch](luis-concept-enterprise.md#dispatch-tool-and-model) nebo [kontejneru Luis](luis-container-howto.md).

|Plošný|Omezení|
|--|:--|
| [Název aplikace][luis-get-started-create-app] | \* Znak výchozí maximální |
| Aplikace| 500 aplikací na prostředek pro vytváření obsahu Azure |
| [Dávkové testování][batch-testing]| 10 datové sady, 1000 projevy na datovou sadu|
| Explicitní seznam | 50 na aplikaci.|
| Externí entity | žádná omezení |
| [Záměry][intents]|500 na aplikaci: 499 vlastní záměry a požadovaný záměr _none_ .<br>Aplikace [založená na odesílání](https://aka.ms/dispatch-tool) má odpovídající 500 zdrojů odesílání.|
| [Seznam entit](./luis-concept-entity-types.md) | Nadřízený: 50, podřízený: 20 000 položek. Název v kanonickém tvaru je * výchozí maximální počet znaků. Synonymum hodnoty mají žádné omezení délky. |
| Entity, které se [naučily počítačem + role](./luis-concept-entity-types.md):<br> Náhled<br>pouh<br>role entity|Limit buď pro 100 nadřazených entit, nebo na 330 entit, podle toho, které uživatele omezují napřed. Role se počítá jako entita pro účely této hranice. Příklad je složený s jednoduchou entitou, která má 2 role: 1 složený + 1 jednoduché + 2 role = 4 entit 330.<br>Dílčí součásti můžou být vnořené až o 5 úrovní.|
|Model jako funkce| Maximální počet modelů, které lze použít jako popisovač (funkce) pro určitý model na 10 modelů. Maximální počet seznamů frází použitých jako popisovač (funkce) pro určitý model má být 10 frázových seznamů.|
| [Náhled – entity dynamického seznamu](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 seznamy ~ 1 tisíc na požadavek koncového bodu předpovědi dotazu|
| [Charakteristiky](luis-concept-patterns.md)|500 vzory na jednu žádost.<br>Maximální délka vzor je až 400 znaků.<br>3 entity Pattern.any za vzor<br>Maximálně 2 vnořené volitelné texty ve vzoru|
| [Vzor. any](./luis-concept-entity-types.md)|100 na aplikaci, 3 entity pattern.any za vzor |
| [Seznam frází][phrase-list]|seznamy frází 500. PhraseList bez možnosti změny má maximálně 5 000 frází. Zaměnitelné PhraseList má maximálně 50 000 frází. Maximální počet všech frází na aplikaci 500 000 frází|
| [Předem připravené entity](./luis-prebuilt-entities.md) | bez omezení|
| [Entity regulárních výrazů](./luis-concept-entity-types.md)|20 entity<br>Max. 500 znaků. každý vzor regulárního výrazu entity|
| [Role](luis-concept-roles.md)|300 role na aplikaci. 10 rolemi na entitu|
| [Utterance][utterances] | 500 znaků|
| [Projevy][utterances] | 15 000 na aplikaci – počet projevy na záměr není nijak omezený.|
| [Zachovávaných](luis-concept-version.md)| verze 100 na aplikaci |
| [Název verze][luis-how-to-manage-versions] | 10 znaků omezen na alfanumerické znaky a tečky (.) |

\* Znak výchozí maximální počet je 50 znaků.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Jedinečnost názvu

Použijte následující pravidla jedinečnosti pro pojmenování.

Následující musí být jedinečné v rámci aplikace LUIS:

* Název verze
* úmysl
* entita
* role

Následující musí být jedinečné v rámci použité oblasti:

* seznam frází

## <a name="object-naming"></a>Pojmenovávání objektů

V následujících názvech nepoužívejte následující znaky.

|Objekt|Vyloučit znaky|
|--|--|
|Názvy záměrů, entit a rolí|`:`<br>`$` <br> `&`|
|Název verze|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Použití klíče

Porozumění jazyku má samostatné klíče, jeden typ pro vytváření obsahu a jeden typ pro dotazování koncového bodu předpovědi. Další informace o rozdílech mezi typy klíčů najdete v tématu [vytváření klíčů koncových bodů a předpovědi dotazů v Luis](luis-concept-keys.md).

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>Omezení klíčů prostředků

Klíče prostředků mají odlišná omezení pro vytváření a koncový bod. Klíč koncového bodu dotazu předpovědi LUIS je platný jenom pro dotazy koncového bodu.

* 500 aplikací na prostředek pro vytváření obsahu Azure

|Klíč|Vytváření obsahu|Koncový bod|Účel|
|--|--|--|--|
|Starter|1 milion za měsíc, 5 za sekundu|1 tisíc za měsíc, 5 za sekundu|Vytváření aplikace LUIS|
|F0 – úroveň Free |1 milion za měsíc, 5 za sekundu|10 tisíc za měsíc, 5 za sekundu|Dotazování na koncový bod služby LUIS|
|S0 – úroveň Basic|-|50/s|Dotazování na koncový bod služby LUIS|
|S0 – úroveň Standard|-|50/s|Dotazování na koncový bod služby LUIS|
|[Integrace analýzy mínění](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|Přidání informací mínění včetně extrakce dat klíčové fráze se poskytuje bez vyžadování dalšího prostředku Azure. |
|[Integrace řeči](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|1000 požadavků na koncový bod na jednotkové náklady|Převést utterance mluvené slovo na text utterance a vrátí výsledky LUIS|

[Přečtěte si další informace o cenách.][pricing]

## <a name="keyboard-controls"></a>Ovládacím prvkům klávesnice

|Vstup z klávesnice | Popis |
|--|--|
|Ovládací prvek + E|Přepne mezi tokeny a entit v seznamu projevy|

## <a name="website-sign-in-time-period"></a>Časové období pro přihlášení k webu

Přístup k vašemu přihlašování je určen **60 minut**. Tato chyba se zobrazí po tomto časovém období. Budete se muset znovu přihlásit.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
