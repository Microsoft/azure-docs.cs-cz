---
title: Omezení – LUIS
description: Tento článek obsahuje známé limity Azure Cognitive Services Language Understanding (LUIS). LUIS má několik omezených oblastí. Model omezuje ovládací prvky pro záměry, entity a funkce v LUIS. Omezení kvóty na základě typu klíče. Kombinace kláves řídí web LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 06/04/2020
ms.openlocfilehash: 41423ce34a62dfdbd5b9a60f683a2366a94d1bfd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97976788"
---
# <a name="limits-for-your-luis-model-and-keys"></a>Omezení pro LUIS model a klíče
LUIS má několik omezených oblastí. První je [limit modelu](#model-limits), který řídí záměry, entity a funkce v Luis. Druhá oblast má [omezení kvóty](#key-limits) na základě typu klíče. Třetí oblastí omezení je [kombinace kláves](#keyboard-controls) pro řízení webu Luis. Čtvrtá oblast je [mapování světové oblasti](luis-reference-regions.md) mezi vytvářením webu Luis a rozhraními API [koncového bodu](luis-glossary.md#endpoint) Luis.

<a name="model-boundaries"></a>

## <a name="model-limits"></a>Omezení modelu

Pokud vaše aplikace překračuje omezení modelu LUIS, zvažte použití aplikace [Luis Dispatch](luis-concept-enterprise.md#dispatch-tool-and-model) nebo [kontejneru Luis](luis-container-howto.md).

|Plošný|Omezení|
|--|:--|
| [Název aplikace][luis-get-started-create-app] | * Výchozí znak maxima |
| Aplikace| 500 aplikací na prostředek pro vytváření obsahu Azure |
| [Dávkové testování][batch-testing]| 10 datových sad, 1000 projevy na datovou sadu|
| Explicitní seznam | 50 na aplikaci|
| Externí entity | žádná omezení |
| [Záměry][intents]|500 na aplikaci: 499 vlastní záměry a požadovaný záměr _none_ .<br>Aplikace [založená na odesílání](https://aka.ms/dispatch-tool) má odpovídající 500 zdrojů odesílání.|
| [Vytvoření seznamu entit](./luis-concept-entity-types.md) | Nadřazený objekt: 50, podřízené položky: 20 000. Kanonický název je * výchozí hodnota Maximum znaků. Hodnoty synonym nemají omezení délky. |
| [entity strojového učení + role](./luis-concept-entity-types.md):<br> Náhled<br>pouh<br>role entity|Limit buď pro 100 nadřazených entit, nebo na 330 entit, podle toho, které uživatele omezují napřed. Role se počítá jako entita pro účely tohoto limitu. Příklad je složený s jednoduchou entitou, která má 2 role: 1 složený + 1 jednoduché + 2 role = 4 entit 330.<br>Subentity je možné vnořovat až o 5 úrovní s maximálně 10 podřízenými objekty na úrovni.|
|Model jako funkce| Maximální počet modelů, které se dají použít jako funkce pro konkrétní model, je 10 modelů. Maximální počet seznamů frází použitých jako funkce pro určitý model má být 10 frázových seznamů.|
| [Náhled – entity dynamického seznamu](./luis-migration-api-v3.md)|2 seznamy ~ 1 tisíc na požadavek koncového bodu předpovědi dotazu|
| [Vzory](luis-concept-patterns.md)|500 vzorů na aplikaci<br>Maximální délka vzorku je 400 znaků.<br>3 vzor. jakékoli entity na vzor<br>Maximálně 2 vnořených volitelných textů ve vzoru|
| [Vzor. any](./luis-concept-entity-types.md)|100 na aplikaci, 3 vzor. libovolný počet entit na vzor |
| [Seznam frází][phrase-list]|seznamy frází 500. 10 globálních frázových seznamů z důvodu modelu jako omezení funkce. Seznam frází bez možnosti změny má maximálně 5 000 frází. Seznam frází, který se mění, má maximálně 50 000 frází. Maximální počet všech frází na aplikaci 500 000 frází|
| [Předem připravené entity](./howto-add-prebuilt-models.md) | bez omezení|
| [Entity regulárního výrazu](./luis-concept-entity-types.md)|20 entit<br>maximální počet znaků: 500 pro vzor entity regulárního výrazu|
| [Role](./luis-concept-entity-types.md)|role 300 na aplikaci 10 rolí na entitu|
| [Výrok][utterances] | 500 znaků<br><br>Pokud máte text delší, než je tento limit znaků, je nutné nejprve segmentovat utterance před vstupem do LUIS a obdržíte jednotlivé reakce na segment. Existují zřejmé přestávky, se kterými můžete pracovat, jako jsou například interpunkční znaménka a dlouhá pauza v řeči.|
| [Příklady utterance][utterances] | 15 000 na aplikaci – počet projevy na záměr není nijak omezený.<br><br>Pokud potřebujete vytvořit výuku aplikace s dalšími příklady, použijte přístup k modelu [odeslání](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) . Jednotlivé aplikace LUIS (označované jako podřízené aplikace pro nadřazenou aplikaci pro expedici) provedete pomocí jednoho nebo více záměrů a potom zajistěte odeslání aplikace, která se vzorkuje z každého podřízeného projevy aplikace LUIS, aby směrovala požadavek předpovědi na správnou podřízenou aplikaci. |
| [Versions](./luis-concept-app-iteration.md) (Verze)| verze 100 na aplikaci |
| [Název verze][luis-how-to-manage-versions] | 128 znaků |

* Výchozí znak maxima je 50 znaků.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Jedinečnost názvu

Názvy objektů musí být jedinečné v porovnání s jinými objekty stejné úrovně.

|Objekty|Omezení|
|--|--|
|Záměr, entita|Všechny názvy záměrů a entit musí být ve verzi aplikace jedinečné.|
|Komponenty entity ML|Všechny komponenty entity strojového učení (podřízené entity) musí být jedinečné v rámci této entity pro komponenty na stejné úrovni.|
|Funkce | Všechny pojmenované funkce, jako jsou seznamy frází, musí být jedinečné v rámci verze aplikace.|
|Role entit|Všechny role v komponentě entity nebo entity musí být jedinečné, pokud jsou na stejné úrovni entity (nadřazená, podřízená a podřízená položka atd.).|

## <a name="object-naming"></a>Pojmenovávání objektů

V následujících názvech nepoužívejte následující znaky.

|Objekt|Vyloučit znaky|
|--|--|
|Názvy záměrů, entit a rolí|`:`<br>`$` <br> `&`|
|Název verze|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Využití a omezení prostředků

Porozumění jazyku má samostatné prostředky, jeden typ pro vytváření obsahu a jeden typ pro dotazování koncového bodu předpovědi. Další informace o rozdílech mezi typy klíčů najdete v tématu [vytváření klíčů koncových bodů a předpovědi dotazů v Luis](luis-how-to-azure-subscription.md).

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Vytváření omezení prostředků

 `LUIS.Authoring` Při filtrování prostředků v Azure Portal použijte druh. LUIS omezuje počet aplikací 500 na prostředek pro vytváření obsahu Azure.

|Vytváření prostředku|Vytváření TPS|
|--|--|
|Starter|1 milion za měsíc, 5 za sekundu|
|F0 – úroveň Free |1 milion za měsíc, 5 za sekundu|

* TPS = transakcí za sekundu

[Přečtěte si další informace o cenách.][pricing]

### <a name="query-prediction-resource-limits"></a>Omezení prostředků předpovědi dotazů

 `LUIS` Při filtrování prostředků v Azure Portal použijte druh. Prostředek koncového bodu předpovědi dotazů LUIS, který se používá v modulu runtime, je platný jenom pro dotazy koncového bodu.

|Prostředek předpovědi dotazu|TPS dotazu|
|--|--|
|F0 – úroveň Free |10 tisíc za měsíc, 5 za sekundu|
|S0 – úroveň Standard|50 za sekundu|

### <a name="sentiment-analysis"></a>Analýza mínění

[Integrace analýzy mínění](luis-how-to-publish-app.md#enable-sentiment-analysis), která poskytuje informace o mínění, je k dispozici bez nutnosti dalšího prostředku Azure.

### <a name="speech-integration"></a>Integrace řeči

[Integrace řeči](../speech-service/how-to-recognize-intents-from-speech-csharp.md) poskytuje 1000 požadavků na koncový bod na jednotkové náklady.

[Přečtěte si další informace o cenách.][pricing]

## <a name="keyboard-controls"></a>Ovládací prvky klávesnice

|Vstup z klávesnice | Description |
|--|--|
|CTRL + E|Přepíná mezi tokeny a entitami v seznamu projevy.|

## <a name="website-sign-in-time-period"></a>Časové období pro přihlášení k webu

Přístup k vašemu přihlašování je určen **60 minut**. Tato chyba se zobrazí po uplynutí tohoto časového období. Budete se muset znovu přihlásit.

[luis-get-started-create-app]: ./luis-get-started-create-app.md
[batch-testing]: ./luis-concept-test.md#batch-testing
[intents]: ./luis-concept-intent.md
[phrase-list]: ./luis-concept-feature.md
[utterances]: ./luis-concept-utterance.md
[luis-how-to-manage-versions]: ./luis-how-to-manage-versions.md
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
