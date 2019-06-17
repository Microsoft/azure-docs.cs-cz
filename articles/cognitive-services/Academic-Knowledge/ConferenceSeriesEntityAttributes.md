---
title: Atributy entit konference řady – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další informace o atributy, které můžete použít s entitou řady místa konání konference.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 38b4aa4c899668a68041f042ce6981ddd8c58219
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340192"
---
# <a name="conference-series-entity"></a>Konference řady Entity

<sub> * Následující atributy jsou specifická pro entity řady místa konání konference. (Ty = "3") </sub>

Název    |Popis                            |Type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |Entity ID                              |Int64      |Je rovno
CN      |Normalizovaný název řady konference      |String     |Je rovno
DCN     |Zobrazovaný název řady konference         |String     |None
CC      |Konference řady celkový počet citace         |Int32      |None  
ECC     |Konference řady citace celkový odhadovaný počet   |Int32      |None
F.FId   |Pole ID entity studie přiřazená k řadě konference |Int64  | Je rovno
F.FN    |Pole Název studie přiřazená k řadě konference  | Je rovno<br/>StartsWith
