---
title: Předem připravené modely – LUIS
titleSuffix: Azure Cognitive Services
description: Předem připravené modely poskytují domén, záměrů, projevy a entity. Můžete spustit vaši aplikaci s využitím předem připravených doménou nebo později přidat příslušné domény do vaší aplikace.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: e0b5b95a7524b60a7c3367035a15a7158fa7908a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280836"
---
# <a name="prebuilt-models"></a>Předem připravené modely

Předem připravené modely poskytují domén, záměrů, projevy a entity. Můžete spustit aplikaci s předem vytvořeným modelem nebo později přidat příslušný model do aplikace. 

## <a name="types-of-prebuilt-models"></a>Typech předem připravené modely

LUIS poskytuje tři typy předem sestavených modelů. Každý model lze přidat do vaší aplikace v každém okamžiku. 

|Typ modelu|Zahrnuje|
|--|--|
|[Domain](luis-reference-prebuilt-domains.md)|Záměrů, projevy, entity|
|Záměry|Záměrů, projevy|
|[Podnikům](luis-reference-prebuilt-entities.md)|Pouze entity| 

## <a name="prebuilt-domains"></a>Předem připravené domény

Language Understanding (LUIS) poskytuje předem *připravené domény*, které jsou předem vyškolenými modely [záměrů](luis-how-to-add-intents.md) a [entit](luis-concept-entity-types.md) , které pracují společně pro domény nebo běžné kategorie klientských aplikací. 

Předem připravených domén jsou trénované a připravené k přidání do aplikace LUIS. Záměry a entity předem sestavené domény jsou po přidání do vaší aplikace plně přizpůsobitelné. 

> [!TIP]
> Záměry a entity v doméně předem připravených fungují nejlépe společně. Je lepší zkombinovat záměry a entity ve stejné doméně, pokud je to možné.
> Doména předem připravených nástrojů má záměrů, které můžete přizpůsobit pro použití v libovolné doméně. Do své aplikace můžete například přidat `Utilities.Repeat` a naučit se rozpoznávat jakékoli akce, které by měl uživatel opakovat. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Změna chování záměr předem připravených domény

Můžete zjistit, že předem připravená doména obsahuje záměru, který je podobný záměru, který chcete mít ve vaší aplikaci LUIS, ale chcete, aby chovat odlišně. Například předem sestavená doména poskytuje `MakeReservation` záměr vytvořit rezervaci v rámci restaurace, ale chcete, aby aplikace použila tento záměr k použití rezervací hotelu. V takovém případě můžete chování tohoto záměru upravit přidáním příkladu projevy do záměru o provedení rezervací hotelu a pak aplikaci znovu naučit. 

Úplný seznam předdefinovaných domén najdete v [odkazech předem sestavené domény](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Předem připravené příkazy

LUIS poskytuje předem sestavené záměry a jejich projevy pro každou ze svých předem sestavených domén. Bez přidání celé domény je možné přidat záměry. Přidání záměru je proces přidání záměru a jeho projevy do aplikace. Název záměru a seznamu utterance lze upravit.  

## <a name="prebuilt-entities"></a>Předem připravené entity

Služba LUIS zahrnuje sadu předem připravených entit pro rozpoznávání běžných typů informací, jako jsou data, časy, čísla, měření a měny. Podpora předem připravených entit se liší podle jazykové verze vaší aplikace LUIS. Úplný seznam předem připravených entit, které LUIS podporuje, včetně podpory podle jazykové verze, najdete v tématu [předem sestavený odkaz na entitu](./luis-reference-prebuilt-entities.md).

Předem připravených entit je obsažen ve vaší aplikaci, jeho předpovědi jsou zahrnuty v publikované aplikaci. Chování předem připravených entit je předem vyškolené a **nelze** je upravit. 

> [!NOTE]
> **předdefinovaná hodnota. DateTime** je zastaralá. Je nahrazená funkcí [**Builtin. datetimeV2**](luis-reference-prebuilt-datetimev2.md), která poskytuje rozpoznávání rozsahů data a času a vylepšené rozpoznávání nejednoznačných dat a časů.

## <a name="next-steps"></a>Další kroky

Naučte se, jak do své aplikace [Přidat předem připravené entity](luis-prebuilt-entities.md) .
