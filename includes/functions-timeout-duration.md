---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198322"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Doba trvání časového času aplikace funkce 

Doba trvání časového ochrna aplikace `functionTimeout` funkce je definována vlastností v souboru projektu [host.json.](../articles/azure-functions/functions-host-json.md#functiontimeout) V následující tabulce jsou uvedeny výchozí a maximální hodnoty v minutách pro plány i různé verze runtime:

| Plánování | Verze runtime | Výchozí | Maximum |
|------|---------|---------|---------|
| Využití | 1.x | 5 | 10 |
| Využití | 2.x | 5 | 10 |
| Využití | 3.x | 5 | 10 |
| Premium | 1.x | 30 | Unlimited |
| Premium | 2.x | 30 | Unlimited |
| Premium | 3.x | 30 | Unlimited |
| App Service | 1.x | Unlimited | Unlimited |
| App Service | 2.x | 30 | Unlimited |
| App Service | 3.x | 30 | Unlimited |

> [!NOTE] 
> Bez ohledu na nastavení časového limitu aplikace funkce je 230 sekund maximální doba, kterou může funkce spuštěná protokolem HTTP trvat pro odpověď na požadavek. Důvodem je [výchozí časový limit nečinnosti služby Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Pro delší dobu zpracování zvažte použití [asynchronní vzor durable functions](../articles/azure-functions/durable/durable-functions-overview.md#async-http) nebo [odložit skutečnou práci a vrátit okamžitou odpověď](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
