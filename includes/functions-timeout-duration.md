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
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198322"
---
## <a name="timeout"></a>Doba trvání časového limitu aplikace Function App 

Doba trvání časového limitu aplikace Function App je definována vlastností `functionTimeout` v souboru projektu [Host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . Následující tabulka ukazuje výchozí a maximální hodnoty v minutách pro oba plány i pro různé verze modulu runtime:

| Plánování | Verze modulu runtime | Výchozí | Maximum |
|------|---------|---------|---------|
| Využití | verze | 5 | 10 |
| Využití | 2.x | 5 | 10 |
| Využití | 3.x | 5 | 10 |
| Premium | verze | 30 | Neomezeno |
| Premium | 2.x | 30 | Neomezeno |
| Premium | 3.x | 30 | Neomezeno |
| App Service | verze | Neomezeno | Neomezeno |
| App Service | 2.x | 30 | Neomezeno |
| App Service | 3.x | 30 | Neomezeno |

> [!NOTE] 
> Bez ohledu na nastavení časového limitu aplikace Functions je 230 sekund maximální doba, po kterou může funkce aktivovaná protokolem HTTP reagovat na požadavek. Důvodem je, že [výchozí časový limit nečinnosti Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Pro delší dobu zpracování zvažte použití [Durable Functions asynchronního vzoru](../articles/azure-functions/durable/durable-functions-overview.md#async-http) nebo [odložte skutečnou práci a vraťte okamžitou odpověď](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
