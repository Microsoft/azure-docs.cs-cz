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
ms.openlocfilehash: 31ff6ae66b13eb5e5abcde94e8d3df7f6e32f63c
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407890"
---
Na jeden Instrumentační klíč to znamená, existují některá omezení počtu metrik a událostí na aplikaci. Omezení závisí na zvoleném [cenovém plánu](https://azure.microsoft.com/pricing/details/application-insights/).

| Prostředek | Výchozí omezení | Poznámka
| --- | --- | --- |
| Celkem dat za den | 100 GB | Objem dat jde snížit nastavením limitu. Pokud potřebujete víc dat, na portálu pro toto omezení můžete navýšit až 1 000 GB. Kapacita větší než 1 000 GB, pošlete e-mail na adresu AIDataCap@microsoft.com.
| Throttling | 32 000 událostí za sekundu | Omezení se měří se po minutách.
| Uchovávání dat | 90 dnů | Tento prostředek je pro funkce [Vyhledávání](../articles/azure-monitor/app/diagnostic-search.md), [Analýza](../articles/azure-monitor/app/analytics.md) a [Průzkumník metrik](../articles/azure-monitor/app/metrics-explorer.md).
| [Vícekrokový test dostupnosti](../articles/azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) – uchování podrobných výsledků | 90 dnů | Tento prostředek poskytuje podrobné výsledky každého kroku.
| Maximální velikost události | 64,000 |
| Délka názvu vlastnosti a metriky | 150 | Zobrazit [zadejte schémata](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Délka řetězce hodnoty vlastnosti | 8 192 | Zobrazit [zadejte schémata](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Délka zprávy trasování a výjimky | 32,768  | Zobrazit [zadejte schémata](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| [Testy dostupnosti](../articles/azure-monitor/app/monitor-web-app-availability.md) – počet na aplikaci | 100 |
| [Profiler](../articles/azure-monitor/app/profiler.md) uchovávání dat | 5 dní |
| [Profiler](../articles/azure-monitor/app/profiler.md) dat odeslaných za den | 10 GB |

Další informace najdete v tématu [Ceny a kvóty ve službě Application Insights](../articles/azure-monitor/app/pricing.md).