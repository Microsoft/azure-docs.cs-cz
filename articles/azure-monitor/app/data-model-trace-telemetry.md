---
title: Datový model Azure Telemetrie Application Insights – sledovací telemetrie | Microsoft Docs
description: Application Insights datový model pro telemetrii trasování
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 855a93d8a6350c625fe0820fae83644aec3459ed
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678121"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Sledovací telemetrie: datový model Application Insights

Telemetrie trasování (v [Application Insights](../../azure-monitor/app/app-insights-overview.md)) představuje `printf` příkazy pro trasování stylu, které jsou prohledávány při hledání textu. `Log4Net`, `NLog` a další textové položky souborů protokolu jsou přeloženy do instancí tohoto typu. Trasování nemá měření jako rozšiřitelnost.

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
