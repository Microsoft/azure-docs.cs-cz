---
title: Předem připravené modely - Entity, záměr, Domain - LUIS
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 2a978665eff2f8aa3eab3c7d70b39bdb61ecfda4
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651863"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>Předem připravené modely entity, domény a záměr

Předem připravené modely poskytují domén, záměrů, projevy a entity. Můžete spustit vaši aplikaci s využitím předem připravených doménou nebo později přidat příslušné domény do vaší aplikace. 

## <a name="types-of-prebuilt-models"></a>Typech předem připravené modely

Existují 3 typy předem připravené modely, které poskytuje služby LUIS. Každý model lze přidat do vaší aplikace v každém okamžiku. 

|Typ modelu|Zahrnuje|
|--|--|
|Domain (Doména)|Záměrů, projevy, entity|
|Záměry|Záměrů, projevy|
|Entity|Pouze entity| 

## <a name="prebuilt-domains"></a>Předem připravených domén

Language Understanding (LUIS) poskytuje *předem připravených domén*, které jsou předem připravených sad [záměry](luis-how-to-add-intents.md) a [entity](luis-concept-entity-types.md) , které pracují společně pro domény nebo běžné kategorie klientské aplikace. 

Předem připravených domén jsou trénované a připravené k přidání do aplikace LUIS. Záměry a entity v doméně předem připravených jsou plně přizpůsobitelné po jejich přidání do vaší aplikace. 

Když spustíte z přizpůsobení celou předem připravených doménu, odstraňte záměry a entity, které vaše aplikace nemusí používat. Můžete také přidat některé záměry a entity na sadu předem připravených domény již poskytuje. Například, pokud používáte **události** předem připravených domény aplikace sportovní událost, můžete přidat entity pro týmy, sportu. Při spuštění [poskytuje projevy](luis-how-to-add-example-utterances.md) LUIS, zahrňte podmínky, které jsou specifické pro vaši aplikaci. Služba LUIS se naučí rozeznat je a přizpůsobuje jim předem připravených domény záměry a entity, které vaše aplikace potřebuje. 

> [!TIP]
> Záměry a entity v doméně předem připravených fungují nejlépe společně. Je lepší zkombinovat záměry a entity ve stejné doméně, pokud je to možné.
> Doména předem připravených nástrojů má záměrů, které můžete přizpůsobit pro použití v libovolné doméně. Například můžete přidat `Utilities.Repeat` do vaší aplikace a trénování rozpoznat libovolné akce uživatele může být vhodné opakování ve vaší aplikaci. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Změna chování záměr předem připravených domény

Můžete zjistit, že předem připravená doména obsahuje záměru, který je podobný záměru, který chcete mít ve vaší aplikaci LUIS, ale chcete, aby chovat odlišně. Například **míst** předem připravená doména poskytuje `MakeReservation` záměr pro provedení rezervace restaurace, ale má vaše aplikace použít k vytvoření hotelových rezervací tohoto záměru. V takovém případě můžete změnit chování tohoto záměru poskytnutím projevy k LUIS informace o vytváření hotelových rezervací a označování je pomocí `MakeReservation` záměru, takže pak LUIS může být retrained rozpoznat `MakeReservation` v žádosti o rezervovat hotelu záměru .

Najdete úplný seznam předem připravených domén v [předem připravených domén odkaz](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Předem připravené příkazy

Služba LUIS poskytuje předem připravených záměry a jejich projevy. Bez přidání celé domény je možné přidat záměry. Přidání záměru je proces přidávání záměru a jeho projevy. Název záměru a seznamu utterance lze upravit.  

## <a name="prebuilt-entities"></a>Předem připravených entit

Služba LUIS zahrnuje sadu předem připravených entit pro rozpoznávání běžných typů informací, jako jsou data, časy, čísla, měření a měny. Podpora předem připravených entit se liší podle jazykové verze vaší aplikace LUIS. Úplný seznam předem připravených entit, které podporuje služba LUIS, včetně podpory podle jazykové verze, najdete v článku [předem připravených entit odkaz](./luis-reference-prebuilt-entities.md).

Předem připravených entit je obsažen ve vaší aplikaci, jeho předpovědi jsou zahrnuty v publikované aplikaci. Chování předem připravených entit je předem vytrénovaných a **nelze** upravit. Postupujte podle těchto kroků, abyste viděli, jak funguje předem připravených entit:

> [!NOTE]
> **Builtin.DateTime** je zastaralý. Se nahrazuje [ **builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), která poskytuje rozpoznávání datum a časové rozsahy a také vylepšené rozpoznávání nejednoznačný dat a časů.

## <a name="next-steps"></a>Další postup

Zjistěte, jak [přidání předem připravených entit](luis-prebuilt-entities.md) do vaší aplikace.