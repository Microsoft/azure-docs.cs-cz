---
title: Vysvětlení, používání rolí v založené na modelu entity
titleSuffix: Azure Cognitive Services
description: Role jsou pojmenované, kontextové podtypy entity použít jenom ve vzorcích. Například v utterance nákup lístků z New Yorku do Londýna Praha i Brno měst jsou ale každý má jiný význam ve větě. New York city původu a Londýn je město cílové.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 033e5e5e054b0a29961ad60e72b1466b51d1df0c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035194"
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

|Vzor s rolemi entity|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


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
