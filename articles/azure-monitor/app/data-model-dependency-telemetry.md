---
title: Azure Application Insights Telemetrie datový Model - Telemetrických závislostí | Dokumentace Microsoftu
description: Application Insights datový model pro telemetrii závislostí
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 3e3d6b8fdc9ac8dd28f73fecd6231e97a5645407
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120083"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetrie závislostí: Datový model Application Insights

Telemetrie závislostí (v [Application Insights](../../azure-monitor/app/app-insights-overview.md)) představuje interakci monitorované součásti pomocí vzdáleného komponenty, jako je SQL nebo koncový bod HTTP.

## <a name="name"></a>Název

Název příkazu zahájené s tímto voláním závislostí. Hodnota s nízkou kardinality. Příklady jsou název uložené procedury a šablonu cesty URL.

## <a name="id"></a>ID

Identifikátor instance volání závislostí. Slouží pro korelaci s položky telemetrie požadavku odpovídající toto volání závislosti. Další informace najdete v tématu [korelace](../../azure-monitor/app/correlation.md) stránky.

## <a name="data"></a>Data

Příkaz iniciovaných toto volání závislosti. Příklady příkazu jazyka SQL a adresa URL protokolu HTTP se všemi parametry dotazu.

## <a name="type"></a>Typ

Název typu závislosti. Kardinalita s nízkou hodnotu pro logické seskupení závislosti a další pole, jako jsou commandName a kód výsledku vyhodnocení. Příklady jsou SQL, Azure table a HTTP.

## <a name="target"></a>Cíl

Cílový web volání závislostí. Příkladem mohou být název serveru, adresa hostitele. Další informace najdete v tématu [korelace](../../azure-monitor/app/correlation.md) stránky.

## <a name="duration"></a>Doba trvání

Doba trvání ve formátu žádosti: `DD.HH:MM:SS.MMMMMM`. Musí být menší než `1000` dnů.

## <a name="result-code"></a>Kód výsledku

Kód výsledku volání závislostí. Příklady kódu chyby SQL a stavový kód protokolu HTTP.

## <a name="success"></a>Úspěch

Údaj o volání úspěšné nebo neúspěšné.

## <a name="custom-properties"></a>Vlastní vlastnosti

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Vlastní měření

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Další postup

- Nastavení pro sledování závislostí [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Nastavení pro sledování závislostí [Java](../../azure-monitor/app/java-agent.md).
- [Napište vlastní závislost telemetrii](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Zobrazit [datový model](data-model.md) pro typy a datový model Application Insights.
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované službou Application Insights.
