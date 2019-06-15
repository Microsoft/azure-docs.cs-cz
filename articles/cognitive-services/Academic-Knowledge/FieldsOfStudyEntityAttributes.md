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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61339581"
---
# <a name="field-of-study-entity"></a>Pole entity studie

<sub> * Následující atributy jsou specifické pro pole entity studie. (Ty = "6") </sub>

Name    |Popis                            |Type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
ID      |Entity ID                              |Int64      |Je rovno
FN      |Pole Normalizovaný název studie         |String     |Je rovno
DFN     |Pole studie zobrazovaný název            |String     |None
CC      |Pole počtu studie celkového citace    |Int32      |None  
ECC     |Pole o celkové odhadované citace počet|Int32      |None
FL      |Úroveň v polích studie hierarchie     |Int32      |Je rovno <br/>IsBetween
FP.FN   |Nadřazené pole Název studie             |String     |Je rovno
FP.FId  |Nadřazené pole ID studie               |Int64      |Je rovno
FC.FN   |Podřízené pole Název studie              |String     |Je rovno
FC.FId  |Podřízené pole ID studie                |Int64      |Je rovno
