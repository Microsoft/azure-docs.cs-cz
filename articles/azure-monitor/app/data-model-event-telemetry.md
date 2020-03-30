---
title: Telemetrie datového modelu Azure Application Insights – telemetrie událostí | Dokumenty společnosti Microsoft
description: Datový model Application Insights pro telemetrii událostí
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: bd8b2581f7642f6825aaf0d1b51c8e94d4333d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671876"
---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetrie událostí: Datový model Application Insights

Můžete vytvořit položky telemetrie událostí (v [Application Insights](../../azure-monitor/app/app-insights-overview.md)) představující událost, ke které došlo ve vaší aplikaci. Obvykle se jedná o interakci uživatele, jako je kliknutí na tlačítko nebo objednávka. Může se také jedná o událost životního cyklu aplikace, jako je inicializace nebo aktualizace konfigurace. 

Sémanticky události mohou nebo nemusí souviset s požadavky. Pokud je však správně použita telemetrie událostí je důležitější než požadavky nebo trasování. Události představují obchodní telemetrii a měly by být předmětem samostatného, méně [agresivního vzorkování](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="name"></a>Name (Název)

Název události. Chcete-li povolit správné seskupení a užitečné metriky, omezte aplikaci tak, aby generovala malý počet samostatných názvů událostí. Nepoužívejte například samostatný název pro každou vygenerovanou instanci události.

Maximální délka: 512 znaků

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Další kroky

- Viz [datový model](data-model.md) pro typy application insights a datový model.
- [Zapsat vlastní telemetrii událostí](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované Application Insights.
