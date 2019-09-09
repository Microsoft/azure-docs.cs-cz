---
title: Omezení – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje známé omezení z Azure Cognitive Services LUIS (Language Understanding). Služba LUIS má několik oblasti hranic. Model hranic řídí záměrů, entit a funkcí v LUIS. Limity kvót podle typu klíče. Kombinace kláves řídí LUIS webu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/9/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: ff267a524001802f8bcd0903fcb7119bab16ef11
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813333"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Hranice pro LUIS model a klíče
Služba LUIS má několik oblasti hranic. První je [modelu hranice](#model-boundaries), který určuje záměrů, entit a funkcí v LUIS. Druhá oblast je [kvóty](#key-limits) podle typu klíče. Je třetí oblasti hranic [klávesové kombinace](#keyboard-controls) pro řízení webu LUIS. Je čtvrtý oblast [mapování oblasti world](luis-reference-regions.md) mezi LUIS vytváření webu a LUIS [koncový bod](luis-glossary.md#endpoint) rozhraní API. 


## <a name="model-boundaries"></a>Model hranice

Pokud vaše aplikace překračuje limity a hranice modelu LUIS, zvažte použití aplikace [Luis Dispatch](luis-concept-enterprise.md#dispatch-tool-and-model) nebo [kontejneru Luis](luis-container-howto.md). 

|Oblast|Omezení|
|--|:--|
| [Název aplikace][luis-get-started-create-app] | \* Znak výchozí maximální |
| Aplikace| 500 aplikací na prostředek Azure |
| [Dávkové testování][batch-testing]| 10 datové sady, 1000 projevy na datovou sadu|
| Explicitní seznam | 50 na aplikaci.|
| Externí entity | žádná omezení |
| [Záměry][intents]|500 na aplikaci: 499 vlastní záměry a požadovaný záměr _none_ .<br>Aplikace [založená na odesílání](https://aka.ms/dispatch-tool) má odpovídající 500 zdrojů odesílání.|
| [Seznam entit](./luis-concept-entity-types.md) | Nadřazený 50, podřízená položka: 20 000 položek. Název v kanonickém tvaru je * výchozí maximální počet znaků. Synonymum hodnoty mají žádné omezení délky. |
| Entity, které se [naučily počítačem + role](./luis-concept-entity-types.md):<br> Náhled<br>pouh<br>role entity|Limit buď pro 100 nadřazených entit, nebo na 330 entit, podle toho, které uživatele omezují napřed. Role se počítá jako entita pro účely této hranice. Příklad je složený s jednoduchou entitou, která má 2 role: 1 složené + 1 jednoduché + 2 role = 4 entit 330.|
| [Náhled – entity dynamického seznamu](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 seznamy ~ 1 tisíc na požadavek koncového bodu předpovědi dotazu|
| [Vzory](luis-concept-patterns.md)|500 vzory na jednu žádost.<br>Maximální délka vzor je až 400 znaků.<br>3 entity Pattern.any za vzor<br>Maximálně 2 vnořené volitelné texty ve vzoru|
| [Pattern.Any](./luis-concept-entity-types.md)|100 na aplikaci, 3 entity pattern.any za vzor |
| [Seznam frází][phrase-list]|10 seznamy frázi, 5 000 položek na seznam|
| [Předem připravených entit](./luis-prebuilt-entities.md) | bez omezení|
| [Regulární výraz entity](./luis-concept-entity-types.md)|20 entity<br>Max. 500 znaků. každý vzor regulárního výrazu entity|
| [Role](luis-concept-roles.md)|300 role na aplikaci. 10 rolemi na entitu|
| [Utterance][utterances] | 500 znaků|
| [Projevy][utterances] | 15 000 na aplikaci – počet projevy na záměr není nijak omezený.|
| [Verze](luis-concept-version.md)| verze 100 na aplikaci |
| [Název verze][luis-how-to-manage-versions] | 10 znaků omezen na alfanumerické znaky a tečky (.) |

\* Znak výchozí maximální počet je 50 znaků. 

<a name="intent-and-entity-naming"></a>

## <a name="object-naming"></a>Pojmenovávání objektů

V následujících názvech nepoužívejte následující znaky.

|Object|Vyloučit znaky|
|--|--|
|Názvy záměrů, entit a rolí|`:`<br>`$`|
|Název verze|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Použití klíče

Porozumění jazyku má samostatné klíče, jeden typ pro vytváření obsahu a jeden typ pro dotazování koncového bodu předpovědi. Další informace o rozdílech mezi typy klíčů najdete v tématu [vytváření klíčů koncových bodů a předpovědi dotazů v Luis](luis-concept-keys.md).

## <a name="key-limits"></a>Omezení klíčů

Vytváření klíč má různá omezení pro vytváření obsahu a koncový bod. Klíč koncového bodu služby LUIS platí pouze pro dotazy na koncový bod.

* 500 aplikací na prostředek Azure 
* verze 100 na aplikaci

|Klíč|Vytváření obsahu|Koncový bod|Účel|
|--|--|--|--|
|Language Understanding Authoring/Starter|1 milion za měsíc, 5 za sekundu|1 tisíc za měsíc, 5 za sekundu|Vytváření aplikace LUIS|
|[Předplatné][pricing] Language Understanding – F0 – úroveň Free |neplatné|10 tisíc za měsíc, 5 za sekundu|Dotazování na koncový bod služby LUIS|
|[Předplatné][pricing] Language Understanding – S0 – úroveň Basic|neplatné|50/s|Dotazování na koncový bod služby LUIS|
|[Předplatné][pricing] služby vnímání – S0 – úroveň Standard|neplatné|50/s|Dotazování na koncový bod služby LUIS|
|[Integrace analýzy mínění](luis-how-to-publish-app.md#enable-sentiment-analysis)|neplatné|bez poplatků|Přidání informací o mínění včetně extrakce klíčových frází |
|[Integrace řeči](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|neplatné|Žádosti koncového bodu 5.50 USD/1 tisíc|Převést utterance mluvené slovo na text utterance a vrátí výsledky LUIS|

## <a name="keyboard-controls"></a>Ovládacím prvkům klávesnice

|Vstup z klávesnice | Popis | 
|--|--|
|Ovládací prvek + E|Přepne mezi tokeny a entit v seznamu projevy|

## <a name="website-sign-in-time-period"></a>Časové období pro přihlášení k webu

Přihlášení přístup je pro **60 minut**. Tato chyba se zobrazí po tomto časovém období. Budete se muset znovu přihlásit.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
