---
title: zahrnout soubor
description: zahrnout soubor
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9c51ce726545d1c64d69c86c36fc69ea43c3b882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279431"
---
Určuje, kolik vyvolání funkcí je agregováno při [výpočtu metrik pro application insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Vlastnost |Výchozí  | Popis |
|---------|---------|---------| 
|batchSize|1000|Maximální počet požadavků na agregaci.| 
|flushTimeout|00:00:30|Maximální doba pro agregaci.| 

Vyvolání funkce jsou agregovány při dosažení první ze dvou limitů.
