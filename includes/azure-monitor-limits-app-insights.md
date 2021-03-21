---
title: zahrnout soubor
description: zahrnout soubor
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 03d83d96bb58ddaf9be4e46c4b62f10e2476e549
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750942"
---
K dispozici jsou určitá omezení počtu metrik a událostí na aplikaci, tj. na klíč instrumentace. Omezení závisí na zvoleném [cenovém plánu](https://azure.microsoft.com/pricing/details/application-insights/).

| Prostředek | Omezení | Poznámka
| --- | --- | --- |
| Celkem dat za den | 100 GB | Objem dat jde snížit nastavením limitu. Pokud potřebujete víc dat, můžete limit na portálu zvýšit, až 1 000 GB. Pro kapacitu větší než 1 000 GB odešlete e-mail na adresu AIDataCap@microsoft.com .
| Throttling | události 32 000 za sekundu | Omezení se měří se po minutách.
| Uchovávání dat | [30-730 dní](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period) | Tento prostředek je pro funkce [Vyhledávání](../articles/azure-monitor/app/diagnostic-search.md), [Analýza](../articles/azure-monitor/logs/log-query-overview.md) a [Průzkumník metrik](../articles/azure-monitor/essentials/metrics-charts.md).
| [Vícekrokový test dostupnosti](../articles/azure-monitor/app/availability-multistep.md) – uchování podrobných výsledků | 90 dnů | Tento prostředek poskytuje podrobné výsledky každého kroku.
| Maximální velikost události | 64 000 bajtů |
| Délka názvu vlastnosti a metriky | 150 | Viz [schémata typů](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Délka řetězce hodnoty vlastnosti | 8 192 | Viz [schémata typů](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Délka zprávy trasování a výjimky | 32 768  | Viz [schémata typů](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| [Testy dostupnosti](../articles/azure-monitor/app/monitor-web-app-availability.md) – počet na aplikaci | 100 |
| Uchovávání dat [profileru](../articles/azure-monitor/app/profiler.md) | 5 dní |
| Data [profileru](../articles/azure-monitor/app/profiler.md) odesílaná za den | 10 GB |

Další informace najdete v tématu [Ceny a kvóty ve službě Application Insights](../articles/azure-monitor/app/pricing.md).