---
title: zahrnout soubor
description: zahrnout soubor
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: dd678fb784fa7587912f113a7c00bebca76efce8
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857351"
---
K dispozici jsou určitá omezení počtu metrik a událostí na aplikaci, tj. na klíč instrumentace. Omezení závisí na zvoleném [cenovém plánu](https://azure.microsoft.com/pricing/details/application-insights/).

| Resource | Výchozí omezení | Poznámka
| --- | --- | --- |
| Celkem dat za den | 100 GB | Objem dat jde snížit nastavením limitu. Pokud potřebujete víc dat, můžete limit na portálu zvýšit, až 1 000 GB. Pro kapacitu větší než 1 000 GB odešlete e-mail na AIDataCap@microsoft.comadresu.
| Omezování | události 32 000 za sekundu | Omezení se měří se po minutách.
| Uchovávání dat | 90 dní | Tento prostředek je pro funkce [Vyhledávání](../articles/azure-monitor/app/diagnostic-search.md), [Analýza](../articles/azure-monitor/app/analytics.md) a [Průzkumník metrik](../articles/azure-monitor/app/metrics-explorer.md).
| [Vícekrokový test dostupnosti](../articles/azure-monitor/app/availability-multistep.md) – uchování podrobných výsledků | 90 dní | Tento prostředek poskytuje podrobné výsledky každého kroku.
| Maximální velikost události | 64,000 |
| Délka názvu vlastnosti a metriky | 150 | Viz [schémata typů](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Délka řetězce hodnoty vlastnosti | 8 192  | Viz [schémata typů](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Délka zprávy trasování a výjimky | 32 768  | Viz [schémata typů](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| [Testy dostupnosti](../articles/azure-monitor/app/monitor-web-app-availability.md) – počet na aplikaci | 100 |
| [](../articles/azure-monitor/app/profiler.md) Uchovávání dat profileru | 5 dní |
| [](../articles/azure-monitor/app/profiler.md) Data profileru odesílaná za den | 10 GB |

Další informace najdete v tématu [Ceny a kvóty ve službě Application Insights](../articles/azure-monitor/app/pricing.md).