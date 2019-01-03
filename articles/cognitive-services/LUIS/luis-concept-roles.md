---
title: Role pro entity
titleSuffix: Azure Cognitive Services
description: Role jsou pojmenované, kontextové podtypy entity použít jenom ve vzorcích. Například v utterance `buy a ticket from New York to London`, jsou města New York i Londýn, ale každý má jiný význam ve větě. New York city původu a Londýn je město cílové.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: cb155486365ffa1beb4657e2d9cc56fcf143b624
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547741"
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


[!INCLUDE [H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="example-role-for-entities"></a>Příklad role pro entity

Role je právě kontextově zjištěná umístění entity v rámci utterance. Je největší efektivity dosáhnete, když utterance má více než jeden tohoto typu entity. K rozlišení mezi je nejjednodušší příklad pro jakýkoli typ entity do a z umístění. Umístění může být reprezentován na mnoho entit různých typů. 

Případy použití příklad přenáší zaměstnanec z jednoho oddělení na jiný kde každé oddělení je položka v seznamu. Příklad: 

`Move [PersonName] from [Department:from] to [Department:to]`. 

Vrácené predikce vrátí se obě oddělení entity v odpovědi JSON a každý bude obsahovat název role. 

## <a name="roles-with-prebuilt-entities"></a>Role s předem vytvořenými entitami

Použijte role s předem vytvořenými entitami sdělit význam různé instance předem připravených entit v rámci utterance. 

### <a name="roles-with-datetimev2"></a>Role s datetimeV2

Předem připravených entit datetimeV2, dělá skvělou úlohy s principy širokou škálu různých v data a časy projevy. Můžete zadat data a rozsahy kalendářních dat jiným způsobem než výchozí Principy předem připravených entit. 

## <a name="next-steps"></a>Další postup

* Zjistěte, jak přidat [role](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
