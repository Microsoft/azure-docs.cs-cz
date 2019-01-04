---
title: Azure Application Insights Telemetrie datový Model - Telemetrie výjimek | Dokumentace Microsoftu
description: Application Insights datový model pro telemetrie výjimek
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
ms.openlocfilehash: eaba4572d9b7a83d27c98cf83eeef8fbd699c467
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811955"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetrie výjimek: Datový model Application Insights

V [Application Insights](../../application-insights/app-insights-overview.md), představuje instanci výjimky zpracované nebo neošetřené výjimky, ke které došlo během provádění monitorovanou aplikaci.

## <a name="problem-id"></a>ID problému

Identifikátor, kde byla výjimka vyvolána v kódu. Používá se pro výjimky seskupení. Obvykle kombinace typu výjimky a funkci ze zásobníku volání.

Maximální délka: 1024 znaků.

## <a name="severity-level"></a>Úroveň závažnosti

Trasujte úroveň závažnosti. Hodnota může být `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Podrobnosti výjimky

(Chcete-li rozšířit)

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Další postup

- Zobrazit [datový model](data-model.md) pro typy a datový model Application Insights.
- Zjistěte, jak [Diagnostika výjimky ve vašich webových aplikací pomocí Application Insights](../../azure-monitor/app/asp-net-exceptions.md).
- Podívejte se na [platformy](../../application-insights/app-insights-platforms.md) podporované službou Application Insights.
