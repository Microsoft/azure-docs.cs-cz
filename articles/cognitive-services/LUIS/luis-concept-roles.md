---
title: Role pro entity
titleSuffix: Azure Cognitive Services
description: Role jsou pojmenované, kontextové podtypy entity použít jenom ve vzorcích. Například v utterance `buy a ticket from New York to London`, jsou města New York i Londýn, ale každý má jiný význam ve větě. New York city původu a Londýn je město cílové.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 5fa922cb91d34483256faf4dcf70569aa2f17b97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813838"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Entita role pro kontextové subtypes

Role umožňují entity, které mají pojmenované podtypy. Roli můžete používat s libovolného typu předem připravená nebo vlastní entity a použít v příkladu projevy a vzory. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Počítače zjištěné entity příklad rolí

V utterance "nákupu lístků ze **New York** k **Londýn**, jsou města New York i Londýn, ale každý má jiný význam ve větě. New York city původu a Londýn je město cílové. 

```
buy a ticket from New York to London
```

Role zadejte název těchto rozdílů:

|Typ entity|Název entity|Role|Účel|
|--|--|--|--|
|Jednoduchý|Location|počátek|kde opustí rovině z|
|Jednoduchý|Location|cíl|kde jsou rovina|

## <a name="non-machine-learned-entity-example-of-roles"></a>Příklad se naučili mimo počítač entity rolí

V utterance "Naplánovat schůzku z 8 až 9", obě čísla určují dobu, ale pokaždé, když má odlišný význam v utterance. Role, zadejte název pro rozdíly. 

```
Schedule the meeting from 8 to 9
```

|Typ entity|Název role|Hodnota|
|--|--|--|
|Předem připravené datetimeV2|StartTime|8|
|Předem připravené datetimeV2|Koncový čas|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Jsou více entit v utterance totéž jako role? 

Více entit může existovat v utterance a může být extrahována bez použití rolí. Pokud kontextu věty označuje s verze entity má hodnotu, pak by měl sloužit roli. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Nepoužívejte duplicitní položky bez význam role

Pokud utterance obsahuje seznam umístění, `I want to travel to Seattle, Cairo, and London.`, toto je seznam, kde každá položka nemá další význam. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Použijte role-li význam zjištěno duplicitní položky

Pokud utterance obsahuje seznam umístění s významem, `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, tato význam o původu, layover a určení by se měly zachytávat s rolemi.

### <a name="roles-can-indicate-order"></a>Role můžete určit pořadí

Pokud utterance změnili označující pořadí, ve kterém chcete extrahovat, `I want to first start with Seattle, second London, then third Cairo`, můžete rozbalit v několika způsoby. Tokeny, které označují roli, můžete označit `first start with`, `second`, `third`. Můžete také použít předem připravených entit **pořadí** a **GeographyV2** předem připravených entit složený entity k zachycení představu o pořadí a místo. 

## <a name="how-are-roles-used-in-example-utterances"></a>Jak se používají role v příkladu projevy?

Pokud entita má roli uživatele a entita je označena jako v utterance příkladu, budete mít možnost výběru pouze entity, nebo výběrem entity a role. 

Následující příklad projevy používat entity a role:

|Token zobrazení|Zobrazení entity|
|--|--|
|Jmenuji se dozvědět více o zajímavých **Praha**|Jmenuji se chcete dozvědět více o {Location}|
|Koupit lístek ze Seattlu do New York|Koupit lístek z {umístění: Origin} a {umístění: Destination}|

## <a name="how-are-roles-related-to-hierarchical-entities"></a>Jak spolu souvisí role a hierarchické entity?

Role jsou teď k dispozici pro všechny entity v příkladu projevy, jakož i předchozí použití vzorů. Protože jsou k dispozici všude, nahrazují nutnost hierarchické entity. Nové entity musí být vytvořené s rolemi, namísto použití hierarchické entity. 

Nakonec se přestanou hierarchické entity.

## <a name="how-are-roles-used-in-patterns"></a>Jak se používají role ve vzorech?
Role v utterance vzor šablony, se používají v rámci utterance: 

|Vzor s rolemi entity|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Syntaxe role ve vzorcích
Entity a role jsou uzavřeny v závorkách, `{}`. Entity a role jsou odděleny dvojtečkou. 

## <a name="entity-roles-versus-collaborator-roles"></a>Entity role a role spolupracovníka

Entita role se vztahují na datový model aplikace LUIS. [Spolupracovník](luis-concept-collaborator.md) rolí platí pro úrovně přístupu pro vytváření. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Další postup

* Použití [praktické kurzu](tutorial-entity-roles.md) entit rolí pomocí jiných počítačů zjistili entity
* Zjistěte, jak přidat [role](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
