---
title: Atributy entit pole studie – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další atributy, které můžete použít pole studie entit v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 862fd6d506d5f1ca6f7f532f80f53a29200f33db
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900423"
---
# <a name="field-of-study-entity"></a>Pole entity studie

<sub> * Následující atributy jsou specifické pro pole entity studie. (Ty = "6") </sub>

Název    |Popis                            |Typ       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
FN      |Pole Normalizovaný název studie         |Řetězec     |Rovná se
DFN     |Pole studie zobrazovaný název            |Řetězec     |žádný
Kopie      |Pole počtu studie celkového citace    |Datový typ Int32      |žádný  
ECC     |Pole o celkové odhadované citace počet|Datový typ Int32      |žádný
USA      |Úroveň v polích studie hierarchie     |Datový typ Int32      |Je rovno <br/>IsBetween
FP. FN   |Nadřazené pole Název studie             |Řetězec     |Rovná se
FP. FId  |Nadřazené pole ID studie               |Int64      |Rovná se
FC. FN   |Podřízené pole Název studie              |Řetězec     |Rovná se
FC. FId  |Podřízené pole ID studie                |Int64      |Rovná se