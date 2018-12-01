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
ms.openlocfilehash: f87b2d1bd105a9a9b8eabb3f2f6686c36f71e6df
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721727"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetrie trasování: datový model Application Insights

Telemetrie trasování (v [Application Insights](app-insights-overview.md)) představuje `printf` stylu příkazy trasování, které jsou prohledávány text. `Log4Net`, `NLog`, a další položky založený na textu protokolu jsou přeloženy do instance tohoto typu. Trasování nemá měření jako rozšiřitelnosti.

## <a name="message"></a>Zpráva

Trasovací zprávy.

Maximální délka: 32768 znaků

## <a name="severity-level"></a>Úroveň závažnosti

Trasujte úroveň závažnosti. Hodnota může být `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Další postup

- [Prozkoumejte protokoly trasování .NET ve službě Application Insights](app-insights-asp-net-trace-logs.md).
- [Prozkoumejte protokoly trasování v Application Insights Java](app-insights-java-trace-logs.md).
- Zobrazit [datový model](application-insights-data-model.md) pro typy a datový model Application Insights.
- [Napište vlastní trasovací telemetrii](app-insights-api-custom-events-metrics.md#tracktrace)
- Podívejte se na [platformy](app-insights-platforms.md) podporované službou Application Insights.
