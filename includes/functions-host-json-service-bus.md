---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0a3f739bae3ec758c8e25d581d3e2b9feb4af5b1
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133627"
---
```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|maxConcurrentCalls|16|Maximální počet souběžných volání zpětného volání, které by mělo zahájit pumpu zpráv. Ve výchozím nastavení modul runtime služby Functions zpracovávat více zpráv souběžně. Chcete-li řídit modul runtime najednou zpracovat pouze jedné frontě nebo tématu zprávy, nastavte `maxConcurrentCalls` na hodnotu 1. | 
|prefetchCount|neuvedeno|Výchozí PrefetchCount, který se použije základní MessageReceiver.| 
|autoRenewTimeout|00:05:00|Maximální doba, ve kterém se automatické obnovení zámku zprávy.| 
