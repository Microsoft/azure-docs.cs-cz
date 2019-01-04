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
ms.openlocfilehash: 0dbc0834d5f5b87a39ae33d296af847cf4e368e3
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977459"
---
Existují některá omezení počtu metrik a událostí na aplikaci (tj. na instrumentační klíč). Omezení závisí na zvoleném [cenovém plánu](https://azure.microsoft.com/pricing/details/application-insights/).

| Prostředek | Výchozí omezení | Poznámka
| --- | --- | --- |
| Celkem dat za den | 100 GB | Objem dat jde snížit nastavením limitu. Pokud potřebujete víc dat, na portálu pro toto omezení můžete navýšit až 1 000 GB. Kapacita větší než 1 000 GB, odeslat e-mail na adresu AIDataCap@microsoft.com.
| Throttling | 32 kB událostí za sekundu | Omezení se měří se po minutách.
| Uchovávání dat | 90 dnů | Tento prostředek je pro funkce [Vyhledávání](../articles/azure-monitor/app/diagnostic-search.md), [Analýza](../articles/azure-monitor/app/analytics.md) a [Průzkumník metrik](../articles/application-insights/app-insights-metrics-explorer.md).
| [Vícekrokový test dostupnosti](../articles/azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) – uchování podrobných výsledků | 90 dnů | Tento prostředek poskytuje podrobné výsledky každého kroku.
| Maximální velikost události | 64 KB | 
| Délka názvu vlastnosti a metriky | 150 | Zobrazit [zadejte schémata](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Délka řetězce hodnoty vlastnosti | 8 192 | Zobrazit [zadejte schémata](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Délka zprávy trasování a výjimky | 10 TISÍC | Zobrazit [zadejte schémata](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| [Testy dostupnosti](../articles/azure-monitor/app/monitor-web-app-availability.md) – počet na aplikaci | 100 |
| [Profiler](../articles/application-insights/app-insights-profiler.md) uchovávání dat | 5 dní |
| [Profiler](../articles/application-insights/app-insights-profiler.md) dat odeslaných za den | 10 GB |

Další informace najdete v tématu [Ceny a kvóty ve službě Application Insights](../articles/application-insights/app-insights-pricing.md).