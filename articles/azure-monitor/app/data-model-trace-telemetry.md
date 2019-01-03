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
ms.openlocfilehash: b777928295d37b0824287d2b9e08526eae29e0f9
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976490"
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
- Podívejte se na [platformy](../../application-insights/app-insights-platforms.md) podporované službou Application Insights.
