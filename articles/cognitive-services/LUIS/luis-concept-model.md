---
title: Návrh s modely – LUIS
description: Jazyk porozumění nabízí několik typů modelů. Některé modely lze použít více než jedním způsobem.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 576ba945018d13db9cd24888f3c41a2215857694
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91316507"
---
# <a name="design-with-intent-and-entity-models"></a>Návrh s využitím a modelů entit

Jazyk porozumění nabízí dva typy modelů pro definování schématu aplikace. Vaše schéma vaší aplikace určuje informace, které dostanete z předpovědi nového uživatele utterance.

Schéma aplikace je sestavené z modelů, které vytvoříte pomocí [strojového výuky](#authoring-uses-machine-teaching):
* [Záměry](#intents-classify-utterances) klasifikovat projevy uživatele
* [Entity](#entities-extract-data) extrahují data z utterance.

## <a name="authoring-uses-machine-teaching"></a>Vytváření obsahu používá počítačové výuky

Metodologie pro LUIS počítače umožňuje snadno naučit koncepty pro počítač. Principy _strojového učení_ není nutné používat Luis. Místo toho se jako učitel sděluje koncept LUIS, který poskytuje příklady konceptu a vysvětluje, jak by měl být koncept modelován pomocí dalších souvisejících konceptů. Jako učitel může také interaktivně zlepšit LUIS model tím, že identifikuje a opravuje chyby předpovědi.

<a name="v3-authoring-model-decomposition"></a>

## <a name="intents-classify-utterances"></a>Záměry klasifikující výroky

Záměr klasifikuje příklad projevy na učení LUIS o záměru. Příklad projevy v rámci záměru slouží jako pozitivní příklady utterance. Stejné projevy se používají jako negativní příklady ve všech dalších záměrech.

Vezměte v úvahu aplikaci, která potřebuje určit záměr uživatele pro seřazení knihy a aplikace, které potřebují dodací adresu pro zákazníka. Tato aplikace má dva záměry: `OrderBook` a `ShippingLocation` .

Následující utterance je **kladný příklad** pro `OrderBook` záměr a **negativní příklad** pro `ShippingLocation` `None` záměr a:

`Buy the top-rated book on bot architecture.`

## <a name="entities-extract-data"></a>Entity extrahující data

Entita představuje jednotku dat, která se má extrahovat z utterance. Entita strojového učení je entita nejvyšší úrovně obsahující subentity, které jsou také entitami strojového učení.

Příkladem entity strojového učení je objednávka pro lístek roviny. V koncepčním případě je to jediná transakce s mnoha menšími jednotkami dat, jako je datum, čas, množství sedadel, typ sedadla, jako je například první třída nebo autokar, umístění původu, cílové umístění a volba krupice.

## <a name="intents-versus-entities"></a>Záměry versus entity

Záměrem je požadovaný výsledek _celého_ utterance, zatímco entity jsou částí dat extrahovaných z utterance. Obvykle jsou záměry svázány s akcemi, které by měla klientská aplikace přijmout. Entity jsou informace potřebné k provedení této akce. Z hlediska programování by záměr aktivoval volání metody a entity budou použity jako parametry pro volání metody.

Tento utterance _musí_ mít záměr a _může_ mít entity:

`Buy an airline ticket from Seattle to Cairo`

Tento utterance má jeden záměr:

* Nákup lístku roviny

Tento utterance _může_ mít několik entit:

* Umístění Seattle (Origin) a Cairo (cíl)
* Množství jediného lístku

## <a name="entity-model-decomposition"></a>Dekompozice modelu entity

LUIS podporuje _rozložení modelu_ s rozhraními API pro vytváření obsahu a rozdělení konceptu na menší části. To vám umožní sestavovat modely s jistotou v tom, jak jsou jednotlivé části sestavené a předpovídané.

Dekompozice modelu má následující části:

* [záměry](#intents-classify-utterances)
    * [funkce](#features)
* [entity strojového učení](reference-entity-machine-learned-entity.md)
    * subentity (také entity strojového učení)
        * [funkce](#features)
            * [seznam frází](luis-concept-feature.md)
            * [entity bez strojového učení](luis-concept-feature.md) , například [regulární výrazy](reference-entity-regular-expression.md), [seznamy](reference-entity-list.md)a [předem připravené entity](luis-reference-prebuilt-entities.md)

<a name="entities-extract-data"></a>
<a name="machine-learned-entities"></a>

## <a name="features"></a>Funkce

[Funkce](luis-concept-feature.md) je rozlišující charakteristické vlastnosti nebo atributy dat, které systém sleduje. Funkce strojového učení poskytují LUIS důležité pomůcky pro hledání věcí, které budou rozlišovat koncept. Jsou to doporučení, která LUIS můžou používat, ale ne pevná pravidla. Tyto pomocné parametry se používají ve spojení s popisky pro hledání dat.

## <a name="patterns"></a>Vzory

[Vzory](luis-concept-patterns.md) jsou navržené tak, aby se zlepšila přesnost, když je několik projevy velmi podobné. Vzor vám umožní získat větší přesnost záměru bez dalších projevy.

## <a name="extending-the-app-at-runtime"></a>Rozšíření aplikace za běhu

Schéma aplikace (modely a funkce) je vyškolené a publikované do koncového bodu předpovědi. K rozšíření předpovědi můžete [předat nové informace](schema-change-prediction-runtime.md)spolu s utterance uživatele do koncového bodu předpovědi.

## <a name="next-steps"></a>Další kroky

* Pochopení [záměrů](luis-concept-intent.md) a [entit](luis-concept-entity-types.md).
* Další informace o [funkcích](luis-concept-feature.md)