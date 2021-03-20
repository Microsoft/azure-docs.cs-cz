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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "77198322"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Doba trvání časového limitu aplikace Function App 

Doba trvání časového limitu aplikace Function App je definována `functionTimeout` vlastností v [host.jsv](../articles/azure-functions/functions-host-json.md#functiontimeout) souboru projektu. Následující tabulka ukazuje výchozí a maximální hodnoty v minutách pro oba plány i pro různé verze modulu runtime:

| Plánování | Verze modulu runtime | Výchozí | Maximum |
|------|---------|---------|---------|
| Využití | verze | 5 | 10 |
| Využití | 2.x | 5 | 10 |
| Využití | 3.x | 5 | 10 |
| Premium | verze | 30 | Unlimited |
| Premium | 2.x | 30 | Unlimited |
| Premium | 3.x | 30 | Unlimited |
| App Service | verze | Unlimited | Unlimited |
| App Service | 2.x | 30 | Unlimited |
| App Service | 3.x | 30 | Unlimited |

> [!NOTE] 
> Bez ohledu na nastavení časového limitu aplikace Functions je 230 sekund maximální doba, po kterou může funkce aktivovaná protokolem HTTP reagovat na požadavek. Důvodem je, že [výchozí časový limit nečinnosti Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Pro delší dobu zpracování zvažte použití [Durable Functions asynchronního vzoru](../articles/azure-functions/durable/durable-functions-overview.md#async-http) nebo [odložte skutečnou práci a vraťte okamžitou odpověď](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
