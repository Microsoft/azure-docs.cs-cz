---
title: Předem připravené modely – LUIS
titleSuffix: Azure Cognitive Services
description: Předem připravené modely poskytují domény, záměry, projevy a entity. Můžete spustit aplikaci s předem vytvořenou doménou nebo přidat relevantní doménu do aplikace později.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 32755ea9dccd2a7d4a0e727c7a006491d5ea3521
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535879"
---
# <a name="prebuilt-models"></a>Předem připravené modely

Předem připravené modely poskytují domény, záměry, projevy a entity. Můžete spustit aplikaci s předem vytvořeným modelem nebo později přidat příslušný model do aplikace. 

## <a name="types-of-prebuilt-models"></a>Typy předem sestavených modelů

LUIS poskytuje tři typy předem sestavených modelů. Každý model lze kdykoli přidat do aplikace. 

|Typ modelu|Zahrnuje|
|--|--|
|[Doména](luis-reference-prebuilt-domains.md)|Záměry, projevy, entity|
|Záměry|Záměry, projevy|
|[Entity](luis-reference-prebuilt-entities.md)|Pouze entity| 

## <a name="prebuilt-domains"></a>Předem připravené domény

Language Understanding (LUIS) poskytuje předem *připravené domény*, které jsou předem vyškolenými modely [záměrů](luis-how-to-add-intents.md) a [entit](luis-concept-entity-types.md) , které pracují společně pro domény nebo běžné kategorie klientských aplikací. 

Předem připravené domény jsou trained a připravené k přidání do aplikace LUIS. Záměry a entity předem sestavené domény jsou po přidání do vaší aplikace plně přizpůsobitelné. 

> [!TIP]
> Záměry a entity v předem připravené doméně pracují nejlépe společně. Je lepší kombinovat záměry a entity ze stejné domény, pokud je to možné.
> Předem vytvořená doména nástrojů má záměry, které můžete přizpůsobit pro použití v libovolné doméně. Můžete například přidat `Utilities.Repeat` do své aplikace a naučit se rozpoznávat jakékoli akce, které by uživatel mohl opakovat ve vaší aplikaci. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Změna chování předem vytvořeného záměru domény

Může se stát, že předem sestavená doména obsahuje záměr podobný záměru, který chcete mít v aplikaci LUIS, ale chcete, aby se chovala jinak. Například předem sestavená **Places** doména poskytuje `MakeReservation` záměr vytvořit rezervaci restaurace, ale chcete, aby aplikace použila tento záměr k použití rezervací hotelu. V takovém případě můžete chování tohoto záměru upravit přidáním příkladu projevy do záměru o provedení rezervací hotelu a pak aplikaci znovu naučit. 

Úplný seznam předdefinovaných domén najdete v [odkazech předem sestavené domény](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Předem připravené záměry

LUIS poskytuje předem sestavené záměry a jejich projevy pro každou ze svých předem sestavených domén. Záměry je možné přidat bez přidání celé domény. Přidání záměru je proces přidání záměru a jeho projevy do aplikace. Název záměru i seznam utterance lze upravit.  

## <a name="prebuilt-entities"></a>Předem připravené entity

LUIS zahrnuje sadu předem připravených entit pro rozpoznávání běžných typů informací, jako jsou data, časy, čísla, měření a měna. Předem připravená podpora entit se liší podle jazykové verze vaší aplikace LUIS. Úplný seznam předem připravených entit, které LUIS podporuje, včetně podpory podle jazykové verze, najdete v tématu [předem sestavený odkaz na entitu](./luis-reference-prebuilt-entities.md).

Pokud je do aplikace zahrnutá předem sestavená entita, je její předpovědi součástí vaší publikované aplikace. Chování předem připravených entit je předem vyškolené a **nelze** je upravit. 

> [!NOTE]
> **předdefinovaná hodnota. DateTime** je zastaralá. Je nahrazená funkcí [**Builtin. datetimeV2**](luis-reference-prebuilt-datetimev2.md), která poskytuje rozpoznávání rozsahů data a času a vylepšené rozpoznávání nejednoznačných dat a časů.

## <a name="next-steps"></a>Další kroky

Naučte se, jak do své aplikace [Přidat předem připravené entity](luis-prebuilt-entities.md) .
