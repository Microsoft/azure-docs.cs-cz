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
ms.openlocfilehash: 0bd66699142e9e03f4a344d499624fe207cb9a45
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963568"
---
## <a name="timeout"></a>Doba trvání časového limitu aplikace Function App 

Doba trvání časového limitu aplikace Function App je definována vlastností functionTimeout v souboru projektu [Host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . Následující tabulka ukazuje výchozí a maximální hodnoty v minutách pro oba plány i pro obě verze modulu runtime:

| Plán | Verze modulu runtime | Výchozí | Maximum |
|------|---------|---------|---------|
| Využití | verze | 5 | 10 |
| Využití | 2.x | 5 | 10 |
| App Service | verze | Unlimited | Unlimited |
| App Service | 2.x | 30 | Unlimited |

> [!NOTE] 
> Bez ohledu na nastavení časového limitu aplikace Functions je 230 sekund maximální doba, po kterou může funkce aktivovaná protokolem HTTP reagovat na požadavek. Důvodem je, že [výchozí časový limit nečinnosti Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Pro delší dobu zpracování zvažte použití [Durable Functions asynchronního vzoru](../articles/azure-functions/durable/durable-functions-overview.md#async-http) nebo odložte [skutečnou práci a vraťte okamžitou odpověď](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
