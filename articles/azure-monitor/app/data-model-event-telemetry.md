---
title: Datový model Azure Telemetrie Application Insights – telemetrie událostí | Microsoft Docs
description: Application Insights datový model pro telemetrii událostí
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: bd8b2581f7642f6825aaf0d1b51c8e94d4333d33
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671876"
---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetrie událostí: datový model Application Insights

Můžete vytvořit položky telemetrie událostí (v [Application Insights](../../azure-monitor/app/app-insights-overview.md)), které reprezentují událost, ke které došlo ve vaší aplikaci. Obvykle jde o interakci uživatele, jako je například kliknutí na tlačítko nebo rezervace objednávky. Může to být také událost životního cyklu aplikace, jako je například inicializace nebo aktualizace konfigurace. 

Sémantika, události mohou nebo nemusí být v souvislosti s požadavky korelační. Je-li však tento příkaz použit správně, telemetrie událostí je důležitější než požadavky nebo trasování. Události reprezentují obchodní telemetrii a měly by být předmětem samostatného a méně agresivního [vzorkování](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="name"></a>Název

Název události Chcete-li povolit správné seskupení a užitečnou metriku, omezte aplikaci tak, aby vygenerovala malý počet samostatných názvů událostí. Nepoužívejte například samostatný název pro každou vygenerovanou instanci události.

Maximální délka: 512 znaků

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Další kroky

- Viz [datový model](data-model.md) pro typy Application Insights a datový model.
- [Zápis vlastní telemetrie událostí](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované nástrojem Application Insights.
