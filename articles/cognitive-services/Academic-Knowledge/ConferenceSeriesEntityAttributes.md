---
title: Atributy entit konference řady – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další informace o atributy, které můžete použít s entitou řady místa konání konference.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f9f28afd7005d7a61aa0d2f4dba69ca598034b52
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900744"
---
# <a name="conference-series-entity"></a>Konference řady Entity

<sub> * Následující atributy jsou specifická pro entity řady místa konání konference. (Ty = "3") </sub>

Název    |Popis                            |Typ       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
CN      |Normalizovaný název řady konference      |Řetězec     |Rovná se
DCN     |Zobrazovaný název řady konference         |Řetězec     |žádný
Kopie      |Konference řady celkový počet citace         |Datový typ Int32      |žádný  
ECC     |Konference řady citace celkový odhadovaný počet   |Datový typ Int32      |žádný
F.FId   |Pole ID entity studie přiřazená k řadě konference |Int64  | Rovná se
F.FN    |Pole Název studie přiřazená k řadě konference  | Je rovno<br/>StartsWith