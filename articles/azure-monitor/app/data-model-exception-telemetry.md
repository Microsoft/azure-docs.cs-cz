---
title: Datový model Azure Telemetrie Application Insights – telemetrie výjimek | Microsoft Docs
description: Application Insights datový model pro telemetrii výjimek
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0ba1c94ee8dc78b937d650cff32e1518a7ca5a12
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677422"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetrie výjimek: datový model Application Insights

V [Application Insights](../../azure-monitor/app/app-insights-overview.md)instance výjimky představuje ošetřenou nebo neošetřenou výjimku, ke které došlo během provádění monitorované aplikace.

## <a name="problem-id"></a>ID problému

Identifikátor, kde byla výjimka vyvolána v kódu. Používá se pro seskupování výjimek. Obvykle se jedná o kombinaci typu výjimky a funkce ze zásobníku volání.

Maximální délka: 1024 znaků

## <a name="severity-level"></a>Úroveň závažnosti

Úroveň závažnosti trasování. Hodnota může být `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

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
