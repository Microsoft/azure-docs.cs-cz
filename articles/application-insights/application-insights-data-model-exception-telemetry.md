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
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 88e521a04d69d4ca169e33a80ac15620568c5282
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642614"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetrie výjimek: datový model Application Insights

V [Application Insights](app-insights-overview.md), představuje instanci výjimky zpracované nebo neošetřené výjimky, ke které došlo během provádění monitorovanou aplikaci.

## <a name="problem-id"></a>ID problému

Identifikátor, kde byla výjimka vyvolána v kódu. Používá se pro výjimky seskupení. Obvykle kombinace typu výjimky a funkci ze zásobníku volání.

Maximální délka: 1024 znaků.

## <a name="severity-level"></a>Úroveň závažnosti

Trasujte úroveň závažnosti. Hodnota může být `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Podrobnosti výjimky

(Chcete-li rozšířit)

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Další postup

- Zobrazit [datový model](application-insights-data-model.md) pro typy a datový model Application Insights.
- Zjistěte, jak [Diagnostika výjimky ve vašich webových aplikací pomocí Application Insights](app-insights-asp-net-exceptions.md).
- Podívejte se na [platformy](app-insights-platforms.md) podporované službou Application Insights.
