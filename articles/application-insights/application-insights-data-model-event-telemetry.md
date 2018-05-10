---
title: Aplikace Azure Statistika Telemetrie datový Model - událostí Telemetrie | Microsoft Docs
description: Aplikace Insights datový model pro událost telemetrie
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin; sergkanz
ms.openlocfilehash: 4f6b5c35b65b4aff2dbe8dafbb2eb07d75c2382a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="event-telemetry-application-insights-data-model"></a>Událost telemetrie: Application Insights datový model

Události můžete vytvořit položky telemetrie (v [Application Insights](app-insights-overview.md)) představují událost, která v aplikaci. Obvykle je zásahu uživatele, jako je tlačítko klikněte na tlačítko nebo pořadí checkout. Může být také událost životní cyklus aplikace jako aktualizace inicializace nebo konfigurace. 

Události sémanticky, může nebo nemusí být korelační na požadavky. Ale pokud používá správně, telemetrie událostí je důležitější než požadavky nebo trasování. Události představují obchodní telemetrie a měl by být subjektu k oddělení, méně agresivní [vzorkování](app-insights-api-filtering-sampling.md).

## <a name="name"></a>Název

Název události. Chcete-li povolit správné seskupení a užitečné metriky, omezte aplikace tak, aby vygeneruje malý počet názvy samostatná událost. Nepoužívejte například samostatné název pro každou instanci vygenerované události.

Maximální délka: 512 znaků

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Další postup

- V tématu [datový model](application-insights-data-model.md) Application Insights typy a data modelu.
- [Psát vlastní události telemetrii](app-insights-api-custom-events-metrics.md#trackevent)
- Podívejte se na [platformy](app-insights-platforms.md) nepodporuje Application Insights.
