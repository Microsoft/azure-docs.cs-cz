---
title: zahrnout soubor
description: zahrnout soubor
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 06/21/2018
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 90de751f416ca611f3c674232c224199ad7af717
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310163"
---
Existují některá omezení počtu metrik a událostí na aplikaci (tj. na instrumentační klíč). Omezení závisí na zvoleném [cenovém plánu](https://azure.microsoft.com/pricing/details/application-insights/).

| Prostředek | Výchozí omezení | Poznámka
| --- | --- | --- |
| Celkem dat za den | 100 GB | Objem dat jde snížit nastavením limitu. Pokud potřebujete další data, můžete zvýšit limit na portálu, až 1 000 GB. Kapacitou větší než 1 000 GB, odesílat e-mailu AIDataCap@microsoft.com.
| Throttling | Události 32 kb/s | Omezení se měří se po minutách.
| Uchovávání dat | 90 dnů | Tento prostředek je pro funkce [Vyhledávání](../articles/application-insights/app-insights-diagnostic-search.md), [Analýza](../articles/application-insights/app-insights-analytics.md) a [Průzkumník metrik](../articles/application-insights/app-insights-metrics-explorer.md).
| [Vícekrokový test dostupnosti](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) – uchování podrobných výsledků | 90 dnů | Tento prostředek poskytuje podrobné výsledky každého kroku.
| Maximální velikost události | 64 KB | 
| Délka názvu vlastnosti a metriky | 150 | V tématu [zadejte schémata](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Délka řetězce hodnoty vlastnosti | 8 192 | V tématu [zadejte schémata](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Délka zprávy trasování a výjimky | 10 TISÍC | V tématu [zadejte schémata](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| [Testy dostupnosti](../articles/application-insights/app-insights-monitor-web-app-availability.md) – počet na aplikaci | 100 |
| [Profileru](../articles/application-insights/app-insights-profiler.md) uchovávání dat | 5 dní |
| [Profileru](../articles/application-insights/app-insights-profiler.md) data odeslaná za den | 10 GB |

Další informace najdete v tématu [Ceny a kvóty ve službě Application Insights](../articles/application-insights/app-insights-pricing.md).