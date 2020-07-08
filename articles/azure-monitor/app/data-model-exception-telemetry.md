---
title: Datový model telemetrie výjimek pro Azure Application Insights
description: Application Insights datový model pro telemetrii výjimek
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: fa9bc4efd9549cbcb1d50439989c5dea79d9d60c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77671932"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetrie výjimek: datový model Application Insights

V [Application Insights](../../azure-monitor/app/app-insights-overview.md)instance výjimky představuje ošetřenou nebo neošetřenou výjimku, ke které došlo během provádění monitorované aplikace.

## <a name="problem-id"></a>ID problému

Identifikátor, kde byla výjimka vyvolána v kódu. Používá se pro seskupování výjimek. Obvykle se jedná o kombinaci typu výjimky a funkce ze zásobníku volání.

Maximální délka: 1024 znaků

## <a name="severity-level"></a>Úroveň závažnosti

Úroveň závažnosti trasování. Hodnota může být `Verbose` , `Information` , `Warning` , `Error` , `Critical` .

## <a name="exception-details"></a>Podrobnosti výjimky

(Bude rozšířeno)

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Další kroky

- Viz [datový model](data-model.md) pro typy Application Insights a datový model.
- Naučte se, jak [diagnostikovat výjimky ve vašich webových aplikacích pomocí Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované nástrojem Application Insights.
