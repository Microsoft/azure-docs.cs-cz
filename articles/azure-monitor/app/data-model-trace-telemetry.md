---
title: Datový model Azure Application Insights – sledovací telemetrie
description: Application Insights datový model pro telemetrii trasování
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8fea4bbf590816b2ef168a2ed16f197389ee282e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320540"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Sledovací telemetrie: datový model Application Insights

Telemetrii trasování (v [Application Insights](./app-insights-overview.md)) představuje `printf` příkazy pro trasování stylu, které jsou prohledávány při hledání textu. `Log4Net`, `NLog` a další položky souborového protokolu založené na textu jsou přeloženy do instancí tohoto typu. Trasování nemá měření jako rozšiřitelnost.

## <a name="message"></a>Zpráva

Trasovat zprávu.

Maximální délka: 32768 znaků

## <a name="severity-level"></a>Úroveň závažnosti

Úroveň závažnosti trasování. Hodnota může být `Verbose` , `Information` , `Warning` , `Error` , `Critical` .

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Další kroky

- [Prozkoumejte protokoly trasování .NET v Application Insights](./asp-net-trace-logs.md).
- [Prozkoumejte protokoly trasování Java v Application Insights](./java-trace-logs.md).
- Viz [datový model](data-model.md) pro typy Application Insights a datový model.
- [Zápis vlastní telemetrie trasování](./api-custom-events-metrics.md#tracktrace)
- Podívejte se na [platformy](./platforms.md) podporované nástrojem Application Insights.

