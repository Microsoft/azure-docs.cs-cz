---
title: Datový model Přehledy aplikací Azure – telemetrie trasování
description: Datový model Application Insights pro telemetrii trasování
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 31958b26cdb8a7897cf0051af6600014c07949fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671949"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetrie trasování: Datový model Application Insights

Trace telemetrie (v `printf` [Application Insights)](../../azure-monitor/app/app-insights-overview.md)představuje příkazy trasování stylu, které jsou prohledávány textem. `Log4Net`, `NLog`a další položky souboru protokolu založené na textu jsou přeloženy do instancí tohoto typu. Trasování nemá měření jako rozšiřitelnost.

## <a name="message"></a>Zpráva

Trasovací zpráva.

Maximální délka: 32768 znaků

## <a name="severity-level"></a>Úroveň závažnosti

Úroveň závažnosti stopy. Hodnota může `Verbose` `Information`být `Warning` `Error`, `Critical`, , .

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Další kroky

- [Prozkoumejte protokoly trasování rozhraní .NET v application insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Prozkoumejte protokoly trasování v jazyce Java v application insights](../../azure-monitor/app/java-trace-logs.md).
- Viz [datový model](data-model.md) pro typy application insights a datový model.
- [Zapsat vlastní telemetrii trasování](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované Application Insights.
