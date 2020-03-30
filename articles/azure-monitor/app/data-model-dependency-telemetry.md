---
title: Datový model závislostí přehledů aplikací Azure Monitor
description: Datový model Application Insights pro telemetrii závislostí
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: ba0d848904d1ba885dc53e2941953d8dfb4864cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671915"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetrie závislostí: Datový model Application Insights

Telemetrie závislostí (v [Application Insights)](../../azure-monitor/app/app-insights-overview.md)představuje interakci monitorované součásti se vzdálenou komponentou, jako je NAPŘÍKLAD SQL nebo koncový bod HTTP.

## <a name="name"></a>Name (Název)

Název příkazu zahájeného tímto voláním závislostí. Nízká hodnota mohutnosti. Příklady jsou uložennázev procedury a šablona cesty URL.

## <a name="id"></a>ID

Identifikátor instance volání závislosti. Používá se pro korelaci s položkou telemetrie požadavku odpovídající tomuto volání závislosti. Další informace naleznete na stránce [korelace.](../../azure-monitor/app/correlation.md)

## <a name="data"></a>Data

Příkaz iniciovaný tímto voláním závislostí. Příklady jsou příkaz SQL a adresa URL HTTP se všemi parametry dotazu.

## <a name="type"></a>Typ

Název typu závislosti. Nízká hodnota mohutnosti pro logické seskupení závislostí a interpretaci jiných polí, jako je commandName a resultCode. Příklady jsou SQL, Tabulka Azure a HTTP.

## <a name="target"></a>Cíl

Cílový web volání závislostí. Příkladem je název serveru, adresa hostitele. Další informace naleznete na stránce [korelace.](../../azure-monitor/app/correlation.md)

## <a name="duration"></a>Doba trvání

Doba trvání požadavku `DD.HH:MM:SS.MMMMMM`ve formátu: . Musí to `1000` být méně než dny.

## <a name="result-code"></a>Kód výsledku

Kód výsledku volání závislostí. Příklady jsou kód chyby SQL a stavový kód HTTP.

## <a name="success"></a>Úspěch

Indikace úspěšného nebo neúspěšného volání.

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Další kroky

- Nastavte sledování závislostí pro [rozhraní .NET](../../azure-monitor/app/asp-net-dependencies.md).
- Nastavte sledování závislostí pro [jazyk Java](../../azure-monitor/app/java-agent.md).
- [Zapsat vlastní telemetrii závislostí](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Viz [datový model](data-model.md) pro typy application insights a datový model.
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované Application Insights.
