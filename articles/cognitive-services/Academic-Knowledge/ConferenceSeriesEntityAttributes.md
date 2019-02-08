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
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884915"
---
# <a name="conference-series-entity"></a>Konference řady Entity

<sub> * Následující atributy jsou specifická pro entity řady místa konání konference. (Ty = "3") </sub>

Název    |Popis                            |Type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
CN      |Normalizovaný název řady konference      |String     |Rovná se
DCN     |Zobrazovaný název řady konference         |String     |žádný
Kopie      |Konference řady celkový počet citace         |Int32      |žádný  
ECC     |Konference řady citace celkový odhadovaný počet   |Int32      |žádný
F.FId   |Pole ID entity studie přiřazená k řadě konference |Int64  | Rovná se
F.FN    |Pole Název studie přiřazená k řadě konference  | Je rovno<br/>StartsWith
