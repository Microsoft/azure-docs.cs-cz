---
title: Atributy entit pole studie – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další atributy, které můžete použít pole studie entit v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: e9d6badf76efd03c0520a728af7b3e47b25f200a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878948"
---
# <a name="field-of-study-entity"></a>Pole entity studie

<sub> * Následující atributy jsou specifické pro pole entity studie. (Ty = "6") </sub>

Název    |Popis                            |Type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |ID entity                              |Int64      |Rovná se
FN      |Pole Normalizovaný název studie         |String     |Rovná se
DFN     |Pole studie zobrazovaný název            |String     |žádný
Kopie      |Pole počtu studie celkového citace    |Int32      |žádný  
ECC     |Pole o celkové odhadované citace počet|Int32      |žádný
FL      |Úroveň v polích studie hierarchie     |Int32      |Je rovno <br/>IsBetween
FP.FN   |Nadřazené pole Název studie             |String     |Rovná se
FP.FId  |Nadřazené pole ID studie               |Int64      |Rovná se
FC.FN   |Podřízené pole Název studie              |String     |Rovná se
FC.FId  |Podřízené pole ID studie                |Int64      |Rovná se
