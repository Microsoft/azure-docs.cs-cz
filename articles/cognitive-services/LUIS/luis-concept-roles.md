---
title: Role pro entity – LUIS
titleSuffix: Azure Cognitive Services
description: Role jsou pojmenované, kontextové podtypy entity použít jenom ve vzorcích. Například v utterance `buy a ticket from New York to London`, jsou města New York i Londýn, ale každý má jiný význam ve větě. New York city původu a Londýn je město cílové.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b4bd61ea74055a04718d8a9d8d5ccd42671af2ac
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638347"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Role entit pro kontextové podtypy

Role umožňují entitám mít pojmenované podtypy. Roli lze použít pro libovolný předem sestavený nebo vlastní typ entity a používá se v obou příkladech projevy a Patterns. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Příklad rolí, které se naučily pro počítač

V utterance "koupit **lístek od Praha** do **Brna**jsou města Praha i Londýn, ale každá z nich má ve větě jiný význam. New York city původu a Londýn je město cílové. 

```
buy a ticket from New York to London
```

Role zadejte název těchto rozdílů:

|Typ entity|Název entity|Role|Účel|
|--|--|--|--|
|Jednoduchý|Location|počátek|kde opustí rovině z|
|Jednoduchý|Location|cíl|kde jsou rovina|

## <a name="non-machine-learned-entity-example-of-roles"></a>Příklad rolí nenáročné na počítač, který se naučil

V utterance "plánování schůzky od 8 do 9" obě čísla označují čas, ale pokaždé má v utterance jiný význam. Role poskytují názvy rozdílů. 

```
Schedule the meeting from 8 to 9
```

|Typ entity|Název role|Hodnota|
|--|--|--|
|Předem připravené datetimeV2|Spuštění|8|
|Předem připravené datetimeV2|EndTime|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Jsou více entit v utterance stejné jako role? 

V utterance může existovat více entit a lze je extrahovat bez použití rolí. Pokud kontext věty označuje, která verze entity má hodnotu, měla by být použita role. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Nepoužívejte role pro duplicity bez významu

Pokud utterance obsahuje seznam umístění, `I want to travel to Seattle, Cairo, and London.`jedná se o seznam, kde každá položka nemá další význam. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Použijte role, pokud duplicitní hodnoty označují význam

Pokud utterance obsahuje seznam míst s významem `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, musí být tento význam původu, layover a cíl zachycen s rolemi.

### <a name="roles-can-indicate-order"></a>Role můžou indikovat pořadí.

Pokud se utterance změnila tak, aby označovala pořadí, které `I want to first start with Seattle, second London, then third Cairo`jste chtěli extrahovat, můžete extrahovat několika způsoby. Můžete označit tokeny, které určují roli, `first start with` `second`,, `third`. V složené entitě můžete také použít předdefinované **pořadí** entit a předem vytvořenou entitu **GeographyV2** a zachytit tak nápad pořadí a umístění. 

## <a name="how-are-roles-used-in-example-utterances"></a>Jak jsou role používané v příkladu projevy?

Když má entita roli a entita je označená v příkladu utterance, máte možnost vybrat jenom entitu nebo vybrat entitu a roli. 

Následující příklad projevy použití entit a rolí:

|Zobrazení tokenu|Zobrazení entity|
|--|--|
|Jsem mi zajímavý, jak se dozvědět víc o **Seattlu**|Zajímá Vás o více informací {Location}|
|Koupit lístek od Seattle do Praha|Koupit lístek z umístění {Location: Origin} do umístění {Location: Destination}|

## <a name="how-are-roles-used-in-patterns"></a>Jak se používají role ve vzorech?
Role v utterance vzor šablony, se používají v rámci utterance: 

|Vzor s rolemi entity|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Syntaxe role ve vzorcích
Entity a role jsou uzavřeny v závorkách, `{}`. Entity a role jsou odděleny dvojtečkou. 

## <a name="entity-roles-versus-collaborator-roles"></a>Role entit versus role spolupracovníka

Role entit se vztahují na datový model aplikace LUIS. [](luis-concept-collaborator.md) Role spolupracovníka se vztahují na úrovně přístupu pro vytváření. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Další postup

* Využijte [praktickou výuku](tutorial-entity-roles.md) s využitím rolí entit s entitami, které se nestrojově naučily.
* Zjistěte, jak přidat [role](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
