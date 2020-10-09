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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76279431"
---
Určuje, kolik vyvolání funkcí je agregované při [výpočtu metrik pro Application Insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Vlastnost |Výchozí  | Description |
|---------|---------|---------| 
|batchSize|1000|Maximální počet požadavků, které se mají agregovat| 
|flushTimeout|00:00:30|Maximální časové období pro agregaci.| 

Volání funkcí jsou agregována při dosažení prvního ze dvou omezení.
