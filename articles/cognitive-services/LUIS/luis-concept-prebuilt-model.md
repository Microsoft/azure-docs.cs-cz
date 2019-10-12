---
title: Předem připravené modely – LUIS
titleSuffix: Azure Cognitive Services
description: Předem připravené modely poskytují domény, záměry, projevy a entity. Můžete spustit aplikaci s předem vytvořenou doménou nebo přidat relevantní doménu do aplikace později.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b88801ded3dea7c7514ff117361feba3e95444ed
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264382"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>Předem připravené modely domén, záměrů a entit

Předem připravené modely poskytují domény, záměry, projevy a entity. Můžete spustit aplikaci s předem vytvořenou doménou nebo přidat relevantní doménu do aplikace později. 

## <a name="types-of-prebuilt-models"></a>Typy předem sestavených modelů

Existují tři typy předem sestavených modelů, které LUIS poskytuje. Každý model lze kdykoli přidat do aplikace. 

|Typ modelu|Zahrnuje|
|--|--|
|Domain (Doména)|Záměry, projevy, entity|
|Záměry|Záměry, projevy|
|Entity|Pouze entity| 

## <a name="prebuilt-domains"></a>Předem připravené domény

Language Understanding (LUIS) poskytuje *předem sestavené domény*, které jsou předem sestavené sady [záměrů](luis-how-to-add-intents.md) a [entit](luis-concept-entity-types.md) , které pracují dohromady pro domény nebo běžné kategorie klientských aplikací. 

Předem připravené domény jsou trained a připravené k přidání do aplikace LUIS. Záměry a entity v předem sestavené doméně jsou po přidání do vaší aplikace plně přizpůsobitelné. 

Pokud začnete s přizpůsobením celé předem připravené domény, odstraňte záměry a entity, které vaše aplikace nemusí používat. Můžete také přidat některé záměry nebo entity do sady, kterou již předem vytvořená doména nabízí. Pokud například používáte **události** předem sestavené pro aplikaci sportovní události, můžete přidat entity pro sportovní týmy. Když začnete [poskytovat projevy](luis-how-to-add-example-utterances.md) do Luis, zahrňte do něj výrazy, které jsou specifické pro vaši aplikaci. LUIS se učí, aby je rozpoznali a vystavili vlastníci domény a entity pro potřeby vaší aplikace. 

> [!TIP]
> Záměry a entity v předem připravené doméně pracují nejlépe společně. Je lepší kombinovat záměry a entity ze stejné domény, pokud je to možné.
> Předem vytvořená doména nástrojů má záměry, které můžete přizpůsobit pro použití v libovolné doméně. Do své aplikace můžete například přidat `Utilities.Repeat` a naučit se rozpoznávat jakékoli akce, které by měl uživatel opakovat. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Změna chování předem vytvořeného záměru domény

Může se stát, že předem sestavená doména obsahuje záměr podobný záměru, který chcete mít v aplikaci LUIS, ale chcete, aby se chovala jinak. Například předem sestavená doména poskytuje `MakeReservation` záměr vytvořit rezervaci restaurace, ale chcete, aby vaše aplikace použila tento záměr na rezervaci hotelu. V takovém případě můžete změnit chování tohoto záměru tím, že projevy LUIS o vytváření rezervací hotelu a jejich označování pomocí @no__tho záměru, takže LUIS se dá přepracovat tak, aby se v požadavku na rezervaci hotelu rozpoznala `MakeReservation`.

Úplný seznam předdefinovaných domén najdete v [odkazech předem sestavené domény](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Předem připravené záměry

LUIS poskytuje předem sestavené záměry a jejich projevy. Záměry je možné přidat bez přidání celé domény. Přidání záměru je proces přidání záměru a jeho projevy. Název záměru i seznam utterance lze upravit.  

## <a name="prebuilt-entities"></a>Předem připravené entity

LUIS zahrnuje sadu předem připravených entit pro rozpoznávání běžných typů informací, jako jsou data, časy, čísla, měření a měna. Předem připravená podpora entit se liší podle jazykové verze vaší aplikace LUIS. Úplný seznam předem připravených entit, které LUIS podporuje, včetně podpory podle jazykové verze, najdete v tématu [předem sestavený odkaz na entitu](./luis-reference-prebuilt-entities.md).

Pokud je do aplikace zahrnutá předem sestavená entita, je její předpovědi součástí vaší publikované aplikace. Chování předem připravených entit je předem vyškolené a **nelze** je upravit. 

> [!NOTE]
> **předdefinovaná hodnota. DateTime** je zastaralá. Je nahrazená funkcí [**Builtin. datetimeV2**](luis-reference-prebuilt-datetimev2.md), která poskytuje rozpoznávání rozsahů data a času a vylepšené rozpoznávání nejednoznačných dat a časů.

## <a name="next-steps"></a>Další kroky

Naučte se, jak do své aplikace [Přidat předem připravené entity](luis-prebuilt-entities.md) .
