---
title: Atributy pole studie v Academic Knowledge API | Microsoft Docs
description: Další atributy, které můžete použít s pole studie entity v Academic Knowledge API v kognitivní služby.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: a8176370f5b2f63b7741f7e892ed5a8c775f19c1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342357"
---
# <a name="field-of-study-entity"></a>Pole studie entity

<sub> * Následující atributy jsou specifické pro pole studie entity. (Ty = '6') </sub>

Název    |Popis                            |Typ       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
FN      |Studie normalizovaný název pole         |Řetězec     |Rovná se
DFN     |Pole studie zobrazovaný název            |Řetězec     |žádný
Kopie      |Pole studie citace celkový počet    |Int32      |žádný  
ECC     |Pole počtu celkový odhadovaný citace|Int32      |žádný
FL      |Úroveň v polích studie hierarchie     |Int32      |Rovná se, <br/>IsBetween
FP. FN   |Nadřazené pole Název studie             |Řetězec     |Rovná se
FP. FId  |Nadřazené pole ID studie               |Int64      |Rovná se
FC. FN   |Podřízené pole Název studie              |Řetězec     |Rovná se
FC. FId  |Podřízené pole ID studie                |Int64      |Rovná se