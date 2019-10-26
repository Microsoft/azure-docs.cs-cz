---
title: Reference k rozhraní API agenta Azure Application Insights
description: Reference k rozhraní API agenta Application Insights. Get-ApplicationInsightsMonitoringConfig. Monitorujte výkon webu bez nutnosti opětovného nasazení webu. Funguje s ASP.NET webovými aplikacemi hostovanými místně, na virtuálních počítačích nebo v Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 91e7bcbe79dfde8bbd4011748b40b692015af1c2
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899738"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>Rozhraní API pro Application Insights agenta: Get-ApplicationInsightsMonitoringConfig

Tento článek popisuje rutinu, která je členem [modulu PowerShellu AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Popis

Načte konfigurační soubor a vytiskne hodnoty do konzoly.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci PowerShellu s oprávněními správce.

## <a name="examples"></a>Příklady

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Parametry

Nejsou vyžadovány žádné parametry.

## <a name="output"></a>Výstup


#### <a name="example-output-from-reading-the-config-file"></a>Příklad výstupu z čtení konfiguračního souboru

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Další kroky

  Zobrazení telemetrických dat:
 - [Prozkoumejte metriky](../../azure-monitor/app/metrics-explorer.md) pro monitorování výkonu a využití.
- [Prohledejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) a Diagnostikujte problémy.
- K pokročilejším dotazům použijte [Analytics](../../azure-monitor/app/analytics.md) .
- [Vytváření řídicích panelů](../../azure-monitor/app/overview-dashboard.md).
 
 Přidání další telemetrie:
 - [Vytvářejte webové testy](monitor-web-app-availability.md) , abyste se ujistili, že vaše lokalita zůstane v provozu.
- [Přidejte telemetrii webového klienta](../../azure-monitor/app/javascript.md) pro zobrazení výjimek z kódu webové stránky a umožnění volání trasování.
- [Přidejte sadu Application Insights SDK do kódu](../../azure-monitor/app/asp-net.md) , abyste mohli vložit trasování a protokolování volání.
 
 Další Application Insights agenta:
 - Pomocí naší příručky můžete [řešit potíže s](status-monitor-v2-troubleshoot.md) agentem Application Insights.
 - Proveďte změny v konfiguraci pomocí rutiny [set Configuration](status-monitor-v2-api-set-config.md) .
