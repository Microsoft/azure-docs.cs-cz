---
title: Datový model telemetrie výjimek pro Azure Application Insights
description: Application Insights datový model pro telemetrii výjimek
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 6732fb3dde79abd5c69e2b0a91710bdd2356d6aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87320591"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetrie výjimek: datový model Application Insights

V [Application Insights](./app-insights-overview.md)instance výjimky představuje ošetřenou nebo neošetřenou výjimku, ke které došlo během provádění monitorované aplikace.

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
- Naučte se, jak [diagnostikovat výjimky ve vašich webových aplikacích pomocí Application Insights](./asp-net-exceptions.md).
- Podívejte se na [platformy](./platforms.md) podporované nástrojem Application Insights.

