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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
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
