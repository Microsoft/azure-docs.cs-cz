---
title: Vysvětlení, používání rolí v založené na modelu entity
titleSuffix: Azure Cognitive Services
description: Role jsou pojmenované, kontextové podtypy entity použít jenom ve vzorcích. Například v utterance nákup lístků z New Yorku do Londýna Praha i Brno měst jsou ale každý má jiný význam ve větě. New York city původu a Londýn je město cílové.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 94a02a06335b35a36c35d4b69776b1eb630b402d
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086018"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Entita role ve vzorech jsou kontextové subtypes
Role jsou pojmenované, kontextové podtypy entity používá pouze v [vzory](luis-concept-patterns.md).

Například v utterance `buy a ticket from New York to London`, jsou města New York i Londýn, ale každý má jiný význam ve větě. New York city původu a Londýn je město cílové. 

Role zadejte název těchto rozdílů:

|Entita|Role|Účel|
|--|--|--|
|Umístění|počátek|kde opustí rovině z|
|Umístění|cíl|kde jsou rovina|
|Předem připravené datetimeV2|na|Koncové datum|
|Předem připravené datetimeV2|od|Počáteční datum|

## <a name="how-are-roles-used-in-patterns"></a>Jak se používají role ve vzorech?
Role v utterance vzor šablony, se používají v rámci utterance: 

|Vzor s rolemi entity|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Syntaxe role ve vzorcích
Entity a role jsou uzavřeny v závorkách, `{}`. Entity a role jsou odděleny dvojtečkou. 


[!include[H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="roles-with-prebuilt-entities"></a>Role s předem vytvořenými entitami

Použijte role s předem vytvořenými entitami sdělit význam různé instance předem připravených entit v rámci utterance. 

### <a name="roles-with-datetimev2"></a>Role s datetimeV2

Předem připravených entit datetimeV2, dělá skvělou úlohy s principy širokou škálu různých v data a časy projevy. Můžete zadat data a rozsahy kalendářních dat jiným způsobem než výchozí Principy předem připravených entit. 

## <a name="next-steps"></a>Další postup

* Zjistěte, jak přidat [role](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
