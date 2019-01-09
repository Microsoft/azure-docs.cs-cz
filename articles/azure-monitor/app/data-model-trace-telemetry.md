---
title: Azure Application Insights Telemetrie datový Model - telemetrická data trasování | Dokumentace Microsoftu
description: Application Insights datový model pro trasovacího telemetrii
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: df85aafc81b199610c02f0faecb06e804fda24bb
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118060"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetrie trasování: Datový model Application Insights

Telemetrie trasování (v [Application Insights](../../azure-monitor/app/app-insights-overview.md)) představuje `printf` stylu příkazy trasování, které jsou prohledávány text. `Log4Net`, `NLog`, a další položky založený na textu protokolu jsou přeloženy do instance tohoto typu. Trasování nemá měření jako rozšiřitelnosti.

## <a name="message"></a>Zpráva

Trasovací zprávy.

Maximální délka: 32768 znaků

## <a name="severity-level"></a>Úroveň závažnosti

Trasujte úroveň závažnosti. Hodnota může být `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Další postup

- [Prozkoumejte protokoly trasování .NET ve službě Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Prozkoumejte protokoly trasování v Application Insights Java](../../azure-monitor/app/java-trace-logs.md).
- Zobrazit [datový model](data-model.md) pro typy a datový model Application Insights.
- [Napište vlastní trasovací telemetrii](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované službou Application Insights.
