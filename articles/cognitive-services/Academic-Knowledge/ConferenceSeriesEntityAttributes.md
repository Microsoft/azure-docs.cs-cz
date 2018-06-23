---
title: Atributy řady konference v Academic Knowledge API | Microsoft Docs
description: Další informace o atributy, které můžete použít s konferenční řady entity v kognitivní služby.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 332736c927bdaa00334546f626a6eabb8e11d3b5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342358"
---
# <a name="conference-series-entity"></a>Konferenční řady Entity

<sub> * Následující atributy jsou specifické pro konferenční řady entity. (Ty = "3") </sub>

Název    |Popis                            |Typ       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
CN      |Normalizovaný název řady konference      |Řetězec     |Rovná se
DCN     |Zobrazovaný název řady konference         |Řetězec     |žádný
Kopie      |Konferenční řady celkový počet citace         |Int32      |žádný  
ECC     |Konferenční řady citace celkový odhadovaný počet   |Int32      |žádný
F.FId   |Pole ID entity studie přiřazená k řadě konference |Int64  | Rovná se
F.FN    |Pole Název studie přiřazená k řadě konference  | Rovná se,<br/>StartsWith