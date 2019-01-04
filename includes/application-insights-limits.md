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
ms.openlocfilehash: 6b9b40c457766573be0d72cb7f4ebe2328a070f5
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54026260"
---
Existují některá omezení počtu metrik a událostí na aplikaci (tj. na instrumentační klíč). Omezení závisí na zvoleném [cenovém plánu](https://azure.microsoft.com/pricing/details/application-insights/).

| Prostředek | Výchozí omezení | Poznámka
| --- | --- | --- |
| Celkem dat za den | 100 GB | Objem dat jde snížit nastavením limitu. Pokud potřebujete víc dat, na portálu pro toto omezení můžete navýšit až 1 000 GB. Kapacita větší než 1 000 GB, odeslat e-mail na adresu AIDataCap@microsoft.com.
| Throttling | 32 kB událostí za sekundu | Omezení se měří se po minutách.
| Uchovávání dat | 90 dnů | Tento prostředek je pro funkce [Vyhledávání](../articles/azure-monitor/app/diagnostic-search.md), [Analýza](../articles/azure-monitor/app/analytics.md) a [Průzkumník metrik](../articles/azure-monitor/app/metrics-explorer.md).
| [Vícekrokový test dostupnosti](../articles/azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) – uchování podrobných výsledků | 90 dnů | Tento prostředek poskytuje podrobné výsledky každého kroku.
| Maximální velikost události | 64 KB | 
| Délka názvu vlastnosti a metriky | 150 | Zobrazit [zadejte schémata](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Délka řetězce hodnoty vlastnosti | 8 192 | Zobrazit [zadejte schémata](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Délka zprávy trasování a výjimky | 10 TISÍC | Zobrazit [zadejte schémata](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| [Testy dostupnosti](../articles/azure-monitor/app/monitor-web-app-availability.md) – počet na aplikaci | 100 |
| [Profiler](../articles/azure-monitor/app/profiler.md) uchovávání dat | 5 dní |
| [Profiler](../articles/azure-monitor/app/profiler.md) dat odeslaných za den | 10 GB |

Další informace najdete v tématu [Ceny a kvóty ve službě Application Insights](../articles/azure-monitor/app/pricing.md).