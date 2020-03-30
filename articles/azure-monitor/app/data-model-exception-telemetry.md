---
title: Telemetrický model telemetrie výjimek Azure Application Insights
description: Datový model Application Insights pro telemetrii výjimek
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: fa9bc4efd9549cbcb1d50439989c5dea79d9d60c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671932"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetrie výjimek: Datový model Application Insights

V [Application Insights](../../azure-monitor/app/app-insights-overview.md), instance Exception představuje zpracovat nebo neošetřené výjimky, ke kterým došlo při provádění monitorované aplikace.

## <a name="problem-id"></a>Id problému

Identifikátor, kde byla vyvolána výjimka v kódu. Používá se pro seskupování výjimek. Obvykle kombinace typu výjimky a funkce ze zásobníku volání.

Maximální délka: 1024 znaků

## <a name="severity-level"></a>Úroveň závažnosti

Úroveň závažnosti stopy. Hodnota může `Verbose` `Information`být `Warning` `Error`, `Critical`, , .

## <a name="exception-details"></a>Podrobnosti výjimky

(Bude prodlouženo)

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Další kroky

- Viz [datový model](data-model.md) pro typy application insights a datový model.
- Přečtěte si, jak [diagnostikovat výjimky ve webových aplikacích pomocí Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované Application Insights.
