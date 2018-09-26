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
ms.openlocfilehash: 062478783465edc2d3afa4b80a22f119e68da049
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091702"
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
