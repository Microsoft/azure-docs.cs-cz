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
ms.openlocfilehash: a80c96891f3d91a920519db2915932742bd84d72
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002293"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetrie trasování: Datový model Application Insights

Telemetrie trasování (v [Application Insights](../../application-insights/app-insights-overview.md)) představuje `printf` stylu příkazy trasování, které jsou prohledávány text. `Log4Net`, `NLog`, a další položky založený na textu protokolu jsou přeloženy do instance tohoto typu. Trasování nemá měření jako rozšiřitelnosti.

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
