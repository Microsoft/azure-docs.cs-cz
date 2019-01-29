---
title: Atributy entit pole studie – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další atributy, které můžete použít pole studie entit v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 793b35d9c6412c40a87f3f91fcd772476d57584f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154563"
---
# <a name="field-of-study-entity"></a>Pole entity studie

<sub> * Následující atributy jsou specifické pro pole entity studie. (Ty = "6") </sub>

Name    |Popis                            |Type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
FN      |Pole Normalizovaný název studie         |Řetězec     |Rovná se
DFN     |Pole studie zobrazovaný název            |Řetězec     |žádný
Kopie      |Pole počtu studie celkového citace    |Datový typ Int32      |žádný  
ECC     |Pole o celkové odhadované citace počet|Datový typ Int32      |žádný
FL      |Úroveň v polích studie hierarchie     |Datový typ Int32      |Je rovno <br/>IsBetween
FP.FN   |Nadřazené pole Název studie             |Řetězec     |Rovná se
FP.FId  |Nadřazené pole ID studie               |Int64      |Rovná se
FC.FN   |Podřízené pole Název studie              |Řetězec     |Rovná se
FC.FId  |Podřízené pole ID studie                |Int64      |Rovná se
