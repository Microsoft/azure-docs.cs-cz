---
title: Pochopení způsobu použití role v Azure na základě vzoru entity - | Microsoft Docs
description: Zjistěte, jak role se používá v entitě na základě vzoru pro pojmenujte podtypem kontextové entity.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: ab6100e33fb767528b87c6afde4c5ef275fc7c81
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35345997"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Entita role v vzory jsou kontextové podtypů
Role jsou pojmenované, kontextové podtypů entity použít pouze v [vzory](luis-concept-patterns.md).

Například v utterance `buy a ticket from New York to London`, New York a Londýnem jsou města, ale každý má jiný význam ve větě. New Yorku je města původ a Londýn města cílový. 

Role pojmenujte tyto rozdíly:

|Entita|Role|Účel|
|--|--|--|
|Umístění|počátek|kde rovině odejde z|
|Umístění|Cílový|kde rovině pojmenováváme|

## <a name="how-are-roles-used-in-patterns"></a>Jak se používají role v vzory?
Role v utterance vzor šablony, se používají v rámci utterance: 

```
buy a ticket from {Location:origin} to {Location:destination}
```

## <a name="role-syntax-in-patterns"></a>Role syntaxi vzory
Entitu a role jsou uvedeny v závorkách, `{}`. Entity a role jsou oddělené dvojtečkou. 

## <a name="roles-versus-hierarchical-entities"></a>Role a hierarchické entity
Hierarchická entity poskytovat stejné kontextové informace jako pro role, ale jenom na utterances v **záměry**. Podobně role poskytují stejné kontextové informace jako hierarchické entity, ale jenom v **vzory**.

|Kontextová učení|Použít v|
|--|--|
|Hierarchická entity|Záměry|
|role|Vzory|

## <a name="next-steps"></a>Další postup

* Informace o postupu přidání [role](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
