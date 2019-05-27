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
ms.openlocfilehash: ffb29fc76313e8870b52cb0a63936da7853ea6ce
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131354"
---
## <a name="timeout"></a>Dobu trvání časového limitu app – funkce 

Dobu trvání časového limitu aplikace function App je definován vlastností functionTimeout v [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) souboru projektu. V následující tabulce jsou uvedeny výchozí a maximální hodnoty během několika minut pro oba plány a obě verze modulu runtime:

| Plánování | Verze modulu runtime | Výchozí | Maximum |
|------|---------|---------|---------|
| Spotřeba | 1.x | 5 | 10 |
| Spotřeba | 2.x | 5 | 10 |
| App Service | 1.x | Neomezené | Neomezené |
| App Service | 2.x | 30 | Neomezené |

> [!NOTE] 
> Bez ohledu na nastavení časového limitu aplikace funkcí, 230 sekund je maximální množství času, které funkci aktivovanou protokolem HTTP učinit v reakci na žádost. Je to z důvodu [výchozí časový limit nečinnosti Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Pro delší dobu zpracování, zvažte použití [Durable Functions asynchronní vzorek](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) nebo [odložit samotnou práci a vrátí okamžitou odezvu](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
