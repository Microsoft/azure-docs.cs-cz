---
title: Předem sestavené modely - LUIS
titleSuffix: Azure Cognitive Services
description: Předem sestavené modely poskytují domény, záměry, projevy a entity. Aplikaci můžete spustit s předem vyestavěnou doménou nebo do ní později přidat příslušnou doménu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280836"
---
# <a name="prebuilt-models"></a>Předem připravené modely

Předem sestavené modely poskytují domény, záměry, projevy a entity. Aplikaci můžete spustit s předem sestaveným modelem nebo do aplikace později přidat relevantní model. 

## <a name="types-of-prebuilt-models"></a>Typy předem předmontovně ných modelů

Služba LUIS poskytuje tři typy předem sestavených modelů. Každý model lze do aplikace kdykoli přidat. 

|Typ modelu|Zahrnuje|
|--|--|
|[Domény](luis-reference-prebuilt-domains.md)|Záměry, projevy, entity|
|Záměry|Záměry, projevy|
|[Entity](luis-reference-prebuilt-entities.md)|Pouze entity| 

## <a name="prebuilt-domains"></a>Předem připravené domény

Language Understanding (LUIS) poskytuje předem vytvořené domény , což jsou předem *vytrénované*modely [záměrů](luis-how-to-add-intents.md) a [entit,](luis-concept-entity-types.md) které spolupracují pro domény nebo běžné kategorie klientských aplikací. 

Předem připravené domény jsou trénované a připravené k přidání do vaší aplikace LUIS. Záměry a entity předem vytvořené domény jsou po přidání do aplikace plně přizpůsobitelné. 

> [!TIP]
> Záměry a entity v předem vytvořené doméně fungují nejlépe společně. Je lepší kombinovat záměry a entity ze stejné domény, pokud je to možné.
> Utilities předem připravená doména má záměry, které můžete přizpůsobit pro použití v libovolné doméně. Můžete například přidat `Utilities.Repeat` do aplikace a trénovat ji rozpoznat všechny akce, které uživatel chtít opakovat ve vaší aplikaci. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Změna chování předem sestaveného záměru domény

Můžete zjistit, že předem sestavená doména obsahuje záměr, který je podobný záměru, který chcete mít v aplikaci LUIS, ale chcete, aby se chovala jinak. Například **místa** předem sestavené `MakeReservation` domény poskytuje záměr pro rezervaci restaurace, ale chcete, aby vaše aplikace použít tento záměr k rezervaci hotelu. V takovém případě můžete změnit chování tohoto záměru přidáním příklad projevy záměru o provádění rezervace hotelu a potom přeškolit aplikaci. 

Úplný seznam předem vytvořených domén naleznete v [odkazu Předem sestavené domény](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Předem sestavené záměry

Služba LUIS poskytuje předem vytvořené záměry a jejich projevy pro každou z jeho předem vytvořených domén. Záměry lze přidat bez přidání celé domény. Přidání záměru je proces přidání záměru a jeho projevy do aplikace. Název záměru a seznam utterance lze změnit.  

## <a name="prebuilt-entities"></a>Předem připravené entity

Služba LUIS obsahuje sadu předem vytvořených entit pro rozpoznávání běžných typů informací, jako jsou data, časy, čísla, měření a měna. Předem vytvořená podpora entit se liší podle jazykové verze vaší aplikace LUIS. Úplný seznam předem vytvořených entit, které podporuje služba LUIS, včetně podpory podle jazykové verze, naleznete v [odkazu na předem připravenou entitu](./luis-reference-prebuilt-entities.md).

Pokud je do vaší aplikace zahrnuta předem vytvořená entita, její předpovědi jsou zahrnuty do publikované aplikace. Chování předem vytvořených entit je předem trénované a **nelze** je změnit. 

> [!NOTE]
> **builtin.datetime** je zastaralý. Je nahrazen [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), který poskytuje uznání data a časové rozsahy, stejně jako lepší rozpoznávání nejednoznačných dat a časů.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak do aplikace [přidat předem vytvořené entity.](luis-prebuilt-entities.md)
