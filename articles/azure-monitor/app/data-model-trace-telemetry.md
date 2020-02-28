---
title: Datový model Azure Application Insights – sledovací telemetrie
description: Application Insights datový model pro telemetrii trasování
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 31958b26cdb8a7897cf0051af6600014c07949fd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671949"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Sledovací telemetrie: datový model Application Insights

Telemetrie trasování (v [Application Insights](../../azure-monitor/app/app-insights-overview.md)) představuje `printf` příkazy pro trasování stylu, které jsou prohledávány při hledání textu. `Log4Net`, `NLog`a další textové položky souborů protokolu jsou přeloženy do instancí tohoto typu. Trasování nemá měření jako rozšiřitelnost.

## <a name="message"></a>Zpráva

Trasovat zprávu.

Maximální délka: 32768 znaků

## <a name="severity-level"></a>Úroveň závažnosti

Úroveň závažnosti trasování. Hodnota může být `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Další kroky

- [Prozkoumejte protokoly trasování .NET v Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Prozkoumejte protokoly trasování Java v Application Insights](../../azure-monitor/app/java-trace-logs.md).
- Viz [datový model](data-model.md) pro typy Application Insights a datový model.
- [Zápis vlastní telemetrie trasování](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované nástrojem Application Insights.
