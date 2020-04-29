---
title: Datový model závislosti Azure Monitor Application Insights
description: Application Insights datový model pro telemetrii závislostí
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: ba0d848904d1ba885dc53e2941953d8dfb4864cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671915"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetrie závislostí: datový model Application Insights

Telemetrie závislostí (v [Application Insights](../../azure-monitor/app/app-insights-overview.md)) představuje interakce monitorované komponenty se vzdálenou komponentou, jako je SQL nebo koncový bod HTTP.

## <a name="name"></a>Název

Název příkazu iniciované tímto voláním závislosti. Nízká hodnota mohutnosti. Příklady jsou název uložené procedury a šablona cesty URL.

## <a name="id"></a>ID

Identifikátor instance volání závislosti Používá se pro korelaci s položkou telemetrie žádosti odpovídající tomuto volání závislosti. Další informace najdete na stránce [korelace](../../azure-monitor/app/correlation.md) .

## <a name="data"></a>Data

Příkaz iniciované tímto voláním závislosti. Příklady jsou příkazy SQL a adresa URL protokolu HTTP se všemi parametry dotazu.

## <a name="type"></a>Typ

Název typu závislosti Nízká hodnota mohutnosti pro logické seskupení závislostí a výklad jiných polí, jako například příkazové a resultCode. Příklady jsou SQL, tabulka Azure a HTTP.

## <a name="target"></a>Cíl

Cílový web volání závislosti. Příkladem je název serveru, adresa hostitele. Další informace najdete na stránce [korelace](../../azure-monitor/app/correlation.md) .

## <a name="duration"></a>Doba trvání

Doba trvání žádosti ve formátu `DD.HH:MM:SS.MMMMMM`:. Musí být kratší než `1000` dny.

## <a name="result-code"></a>Kód výsledku

Kód výsledku volání závislosti Příkladem je kód chyby SQL a kód stavu HTTP.

## <a name="success"></a>Úspěch

Označení úspěšného nebo neúspěšného volání.

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Další kroky

- Nastavte sledování závislostí pro [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Nastavte sledování závislostí pro [Java](../../azure-monitor/app/java-agent.md).
- [Zápis vlastní telemetrie závislosti](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Viz [datový model](data-model.md) pro typy Application Insights a datový model.
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované nástrojem Application Insights.
