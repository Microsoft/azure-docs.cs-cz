---
title: zahrnout soubor
description: zahrnout soubor
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c63cee1c451918569e9b7aa2e76209e8069d86ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92167644"
---
Určuje, kolik vyvolání funkcí je agregované při [výpočtu metrik pro Application Insights](../articles/azure-functions/configure-monitoring.md#configure-the-aggregator). 

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
