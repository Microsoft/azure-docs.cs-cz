---
title: Azure Application Insights Telemetrie datový Model - Telemetrie událostí | Dokumentace Microsoftu
description: Application Insights datový model pro telemetrie událostí
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
ms.openlocfilehash: 777c52276ca767ee1fb4d05666850350c02c1e00
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642772"
---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetrie událostí: datový model Application Insights

Můžete vytvořit událost položky telemetrie (v [Application Insights](app-insights-overview.md)) představující události, ke které došlo ve vaší aplikaci. Obvykle je interakce s uživatelem, například klikněte na tlačítko nebo objednávka rezervace. Může být událost životního cyklu aplikace jako aktualizace inicializace nebo konfigurace. 

Události sémanticky, může nebo nemusí být korelována požadavky. Pokud však správně použít telemetrie událostí je důležitější než požadavky nebo trasování. Události představují obchodní telemetrická data a mělo by být předmětu k oddělení, méně agresivní [vzorkování](app-insights-api-filtering-sampling.md).

## <a name="name"></a>Název

Název události. Povolit správné seskupování a užitečné metriky, zakažte svou aplikaci tak, aby generuje malý počet názvů samostatná událost. Nepoužívejte například odlišný název pro každou instanci vygenerované události.

Maximální délka: 512 znaků

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Další postup

- Zobrazit [datový model](application-insights-data-model.md) pro typy a datový model Application Insights.
- [Psát vlastní telemetrii událostí](app-insights-api-custom-events-metrics.md#trackevent)
- Podívejte se na [platformy](app-insights-platforms.md) podporované službou Application Insights.
