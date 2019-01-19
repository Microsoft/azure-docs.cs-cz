---
title: Omezení
titleSuffix: Language Understanding - Azure Cognitive Services
description: Tento článek obsahuje známé omezení z Azure Cognitive Services LUIS (Language Understanding). Služba LUIS má několik oblasti hranic. Model hranic řídí záměrů, entit a funkcí v LUIS. Limity kvót podle typu klíče. Kombinace kláves řídí LUIS webu.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/18/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b71b62d50f209c033597799dd26f579fcb200cc9
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413360"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Hranice pro LUIS model a klíče
Služba LUIS má několik oblasti hranic. První je [modelu hranice](#model-boundaries), který určuje záměrů, entit a funkcí v LUIS. Druhá oblast je [kvóty](#key-limits) podle typu klíče. Je třetí oblasti hranic [klávesové kombinace](#keyboard-controls) pro řízení webu LUIS. Je čtvrtý oblast [mapování oblasti world](luis-reference-regions.md) mezi LUIS vytváření webu a LUIS [koncový bod](luis-glossary.md#endpoint) rozhraní API. 


## <a name="model-boundaries"></a>Model hranice

|Oblast|Omezení|
|--|:--|--|
| [Název aplikace][luis-get-started-create-app] | * Znak výchozí maximální |
| [Testování služby batch][batch-testing]| 10 datové sady, 1000 projevy na datovou sadu|
| **[Složené](./luis-concept-entity-types.md)|100 s až 10 podřízených objektů |
| Explicitní seznam | 50 na aplikaci.|
| **[Hierarchické](./luis-concept-entity-types.md) |100 s až 10 podřízených objektů |
| [Záměrů][intents]|500 na aplikaci.<br>[Na základě odeslání](https://aka.ms/dispatch-tool) aplikace nemá odpovídající 500 odeslání zdroje|
| [Seznam entit](./luis-concept-entity-types.md) | Nadřazený objekt: 50, podřízené: 20 000 položek. Název v kanonickém tvaru je * výchozí maximální počet znaků. Synonymum hodnoty mají žádné omezení délky. |
| [Vzory](luis-concept-patterns.md)|500 vzory na jednu žádost.<br>Maximální délka vzor je až 400 znaků.<br>3 entity Pattern.any za vzor<br>Maximálně 2 vnořené volitelné texty ve vzoru|
| [Pattern.Any](./luis-concept-entity-types.md)|100 na aplikaci, 3 entity pattern.any za vzor |
| [Seznam frází][phrase-list]|10 seznamy frázi, 5 000 položek na seznam|
| [Předem připravených entit](./luis-prebuilt-entities.md) | bez omezení|
| [Regulární výraz entity](./luis-concept-entity-types.md)|20 entity<br>Max. 500 znaků. každý vzor regulárního výrazu entity|
| [Role](luis-concept-roles.md)|300 role na aplikaci. 10 rolemi na entitu|
| **[Jednoduché](./luis-concept-entity-types.md)| 100 entit|
| [Utterance][utterances] | 500 znaků|
| [Projevy][utterances] | 15 000 na aplikaci.|
| [Verze](luis-concept-version.md)| bez omezení |
| [Název verze][luis-how-to-manage-versions] | 10 znaků omezen na alfanumerické znaky a tečky (.) |

* Znak výchozí maximální počet je 50 znaků. 

** Celkový počet jednoduché, hierarchických a složené entit může mít maximálně 100. Celkový počet hierarchické entity, složený entity, jednoduché entit a hierarchické podřízených entit může mít maximálně 330. 

## <a name="intent-and-entity-naming"></a>Pojmenování entity a záměr
Nepoužívejte tyto znaky v názvech záměr a entity:

|Znak|Název|
|--|--|
|`{`|Levá složená závorka|
|`}`|Pravá složená závorka|
|`[`|Levá závorka|
|`]`|Pravá hranatá závorka|
|`\`|Zpětné lomítko|

## <a name="key-usage"></a>Použití klíče

Principy jazyka má různé klíče, jeden typ určený k vytváření a jeden typ pro dotazování na koncový bod předpovědi. Další informace o rozdílech mezi typy klíčů najdete v tématu [vytváření obsahu a dotaz předpovědi klíče koncového bodu v LUIS](luis-concept-keys.md).

## <a name="key-limits"></a>Omezení klíčů

Vytváření klíč má různá omezení pro vytváření obsahu a koncový bod. Klíč koncového bodu služby LUIS platí pouze pro dotazy na koncový bod.


|Klíč|Vytváření obsahu|Koncový bod|Účel|
|--|--|--|--|
|Vytváření Principy jazyka/Starter|1 milion za měsíc, 5 za sekundu|1 tisíc za měsíc, 5 za sekundu|Vytváření aplikace LUIS|
|Language Understanding [předplatné] [ pricing] - F0 – úroveň Free |neplatné|10 tisíc za měsíc, 5 za sekundu|Dotazování na koncový bod služby LUIS|
|Language Understanding [předplatné] [ pricing] - S0 - na úrovni Basic|neplatné|50/s|Dotazování na koncový bod služby LUIS|
|Služby cognitive Services [předplatné] [ pricing] - S0 - úrovně Standard|neplatné|50/s|Dotazování na koncový bod služby LUIS|
|[Integrace analýzy mínění](luis-how-to-publish-app.md#enable-sentiment-analysis)|neplatné|bez poplatků|Přidání informací o mínění včetně extrakce klíčových frází |
|Integrace rozpoznávání řeči|neplatné|Žádosti koncového bodu 5.50 USD/1 tisíc|Převést utterance mluvené slovo na text utterance a vrátí výsledky LUIS|

## <a name="keyboard-controls"></a>Ovládacím prvkům klávesnice

|Vstup z klávesnice | Popis | 
|--|--|
|Ovládací prvek + E|Přepne mezi tokeny a entit v seznamu projevy|

## <a name="website-sign-in-time-period"></a>Webu přihlásit v časovém období

Přihlášení přístup je pro **60 minut**. Tato chyba se zobrazí po tomto časovém období. Budete muset znovu přihlásit.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
