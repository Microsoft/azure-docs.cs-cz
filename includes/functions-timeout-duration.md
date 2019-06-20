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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175569"
---
## <a name="timeout"></a>Dobu trvání časového limitu app – funkce 

Dobu trvání časového limitu aplikace function App je definován vlastností functionTimeout v [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) souboru projektu. V následující tabulce jsou uvedeny výchozí a maximální hodnoty během několika minut pro oba plány a obě verze modulu runtime:

| Plánování | Verze modulu runtime | Výchozí | Maximum |
|------|---------|---------|---------|
| Využití | 1.x | 5 | 10 |
| Využití | 2.x | 5 | 10 |
| App Service | 1.x | Unlimited | Unlimited |
| App Service | 2.x | 30 | Unlimited |

> [!NOTE] 
> Bez ohledu na nastavení časového limitu aplikace funkcí, 230 sekund je maximální množství času, které funkci aktivovanou protokolem HTTP učinit v reakci na žádost. Je to z důvodu [výchozí časový limit nečinnosti Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Pro delší dobu zpracování, zvažte použití [Durable Functions asynchronní vzorek](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) nebo [odložit samotnou práci a vrátí okamžitou odezvu](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
