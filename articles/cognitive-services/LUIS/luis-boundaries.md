---
title: Hranice jazyk Principy (LEOŠ) | Microsoft Docs
titleSuffix: Azure
description: Tento článek obsahuje známé omezení LEOŠ.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 52bda6a13422ce8f759c40bd454a6b15e92d7a5d
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110292"
---
# <a name="luis-boundaries"></a>LEOŠ hranice
LEOŠ má několik oblasti hranic. První je [modelu hranic](#model-boundaries), který určuje záměry, entit a funkcí v LEOŠ. Druhý oblast je [kvótami](#key-limits) podle typu klíče. Je třetí oblasti hranic [klávesové kombinace](#keyboard-controls) řízení LEOŠ webu. Čtvrtý oblast je [mapování oblasti world](luis-reference-regions.md) mezi LEOŠ vytváření webu a LEOŠ [koncový bod](luis-glossary.md#endpoint) rozhraní API. 


## <a name="model-boundaries"></a>Hranice modelu

|Oblast|Omezení|
|--|:--|--|
| [Název aplikace.][luis-get-started-create-app] | * Znak výchozí maximální |
| [Testování batch][batch-testing]| 10 datové sady, 1000 utterances za datové sady|
| **[Složené](./luis-concept-entity-types.md)|100 s až 10 podřízených prvků |
| Explicitní seznam | 50 na aplikaci.|
| **[Hierarchická](./luis-concept-entity-types.md) |100 s až 10 podřízených prvků |
| [Záměry][intents]|500 na aplikaci.<br>[Na základě odesílání](https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch) aplikace má odpovídající 500 odesílání zdroje|
| [Seznam entity](./luis-concept-entity-types.md) | Nadřazený: 50, podřízený: 20 000 položek. Název v kanonickém tvaru je * výchozí maximální počet znaků. Synonyma mít žádné omezení délky. |
| [Vzory](luis-concept-patterns.md)|500 vzory na aplikaci.<br>Maximální délka vzor je 400 znaků.<br>3 Pattern.any entit na vzor<br>Maximálně 2 vnořené volitelné texty ve vzoru|
| [Pattern.Any](./luis-concept-entity-types.md)|100 na aplikaci, 3 pattern.any entit na vzor |
| [Seznam frází][phrase-list]|10 seznamy frázi, 5 000 položek na seznamu|
| [Předkompilované entity](./Pre-builtEntities.md) | žádné omezení|
| [Regulární výraz entity](./luis-concept-entity-types.md)|20 entity<br>maximální počet 500 znaků. na entity vzor regulárního výrazu|
| [Role](luis-concept-roles.md)|300 role na aplikaci. 10 role na entity|
| **[Jednoduché](./luis-concept-entity-types.md)| 100 entit|
| [utterance][utterances] | 500 znaků|
| [Utterances][utterances] | 15 000 na aplikaci.|
| [Název verze][luis-how-to-manage-versions] | 10 znaků omezen na alfanumerické znaky a období (.) |

* Znak výchozí maximální je 50 znaků. 

** Celkový počet jednoduchý, hierarchických a složené entity nemůže překročit 100. Celkový počet hierarchické, složené entity, jednoduché entity a hierarchické podřízených entit nesmí překročit 330. 

## <a name="intent-and-entity-naming"></a>Záměr a pojmenování entity
V názvech záměr a entity nepoužívejte tyto znaky:

|Znak|Název|
|--|--|
|`{`|Levá složená závorka|
|`}`|Pravé složené závorky|
|`[`|Levé závorky|
|`]`|Pravé závorky|
|`\`|Zpětné lomítko|

## <a name="key-limits"></a>Omezuje klíč
Vytváření klíč má jiné limity pro vytváření obsahu a koncový bod. Klíč koncového bodu služby LEOŠ platí pouze pro koncový bod dotazy.

|Klíč|Vytváření obsahu|Koncový bod|Účel|
|--|--|--|--|
|Vytváření nebo Starter|1 milion za měsíc, 5/druhý|1 tisíc za měsíc, 5/druhý|Vytváření aplikace LEOŠ|
|[Předplatné] [ pricing] - F0 - úroveň Free |neplatné|10 tisíc za měsíc, 5/druhý|Váš koncový bod LEOŠ dotazování|
|[Předplatné] [ pricing] - S0 – základní vrstvě|neplatné|50 za sekundu|Váš koncový bod LEOŠ dotazování|
|[Integrace postojích analýzy](publishapp.md#enable-sentiment-analysis)|neplatné|bez poplatků|Přidání informací o postojích včetně klíče frázi extrakce dat |
|Rozpoznávání řeči integrace|neplatné|Požadavky 5.50 USD/1 tisíc koncový bod|Převést na text utterance mluvené utterance a vrátí výsledky LEOŠ|

## <a name="keyboard-controls"></a>Ovládací prvky klávesnice

|Vstup klávesnice | Popis | 
|--|--|
|Ovládací prvek + E|Přepne mezi tokeny a entity v seznamu utterances|

## <a name="website-sign-in-time-period"></a>Web přihlásit v časovém období.

Přihlášení přístup je pro **60 minut**. Tato chyba se zobrazí po uplynutí této doby. Budete muset znovu přihlásit.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
