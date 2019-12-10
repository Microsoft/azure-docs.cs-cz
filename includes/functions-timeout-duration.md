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
ms.openlocfilehash: fded43bb655cefda508b82eca94522730ab6da00
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941687"
---
## <a name="timeout"></a>Doba trvání časového limitu aplikace Function App 

Doba trvání časového limitu aplikace Function App je definována vlastností functionTimeout v souboru projektu [Host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . Následující tabulka ukazuje výchozí a maximální hodnoty v minutách pro oba plány i pro obě verze modulu runtime:

| Plánujte | Verze modulu runtime | Výchozí | Maximum |
|------|---------|---------|---------|
| Využití | verze | 5 | 10 |
| Využití | 2.x | 5 | 10 |
| Využití | 3.x | 5 | 10 |
| Aplikační služba | verze | Bez omezení | Bez omezení |
| Aplikační služba | 2.x | 30 | Bez omezení |
| Aplikační služba | 3.x | 30 | Bez omezení |

> [!NOTE] 
> Bez ohledu na nastavení časového limitu aplikace Functions je 230 sekund maximální doba, po kterou může funkce aktivovaná protokolem HTTP reagovat na požadavek. Důvodem je, že [výchozí časový limit nečinnosti Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Pro delší dobu zpracování zvažte použití [Durable Functions asynchronního vzoru](../articles/azure-functions/durable/durable-functions-overview.md#async-http) nebo [odložte skutečnou práci a vraťte okamžitou odpověď](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
