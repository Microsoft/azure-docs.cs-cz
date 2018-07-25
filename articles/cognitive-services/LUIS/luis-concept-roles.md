---
title: Vysvětlení, používání role na základě vzoru entit – Azure | Dokumentace Microsoftu
description: Přečtěte si použití role v entity na základě vzoru pojmenovat podtyp kontextové entity.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: diberry
ms.openlocfilehash: d2692cdce9da7428bd7b30c4feaf7347792618f5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222699"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Entita role ve vzorech jsou kontextové subtypes
Role jsou pojmenované, kontextové podtypy entity používá pouze v [vzory](luis-concept-patterns.md).

Například v utterance `buy a ticket from New York to London`, jsou města New York i Londýn, ale každý má jiný význam ve větě. New York city původu a Londýn je město cílové. 

Role zadejte název těchto rozdílů:

|Entita|Role|Účel|
|--|--|--|
|Umístění|počátek|kde opustí rovině z|
|Umístění|cíl|kde jsou rovina|

## <a name="how-are-roles-used-in-patterns"></a>Jak se používají role ve vzorech?
Role v utterance vzor šablony, se používají v rámci utterance: 

```
buy a ticket from {Location:origin} to {Location:destination}
```

## <a name="role-syntax-in-patterns"></a>Syntaxe role ve vzorcích
Entity a role jsou uzavřeny v závorkách, `{}`. Entity a role jsou odděleny dvojtečkou. 

## <a name="roles-versus-hierarchical-entities"></a>Role a hierarchické entity
Hierarchické entity poskytují stejné kontextové informace jako role, ale pouze projevy v **záměry**. Podobně, v těchto rolích zajišťují stejné kontextové informace jako hierarchické entity, ale pouze v **vzory**.

|Kontextové učení|Použít v|
|--|--|
|hierarchické entity|Záměrů|
|role|Vzory|

## <a name="next-steps"></a>Další postup

* Zjistěte, jak přidat [role](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
