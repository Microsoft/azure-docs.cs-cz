---
title: Odkaz na rozhraní API agenta Azure Application Insights
description: Odkaz na rozhraní API agenta Application Insights. Enable-InstrumentationEngine. Sledujte výkon webových stránek bez opětovného nasazení webu. Funguje s ASP.NET webových aplikací hostovaných místně, ve virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b3f298ac31cc584cd16553186359c87f69f27aad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671349"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>Rozhraní API agenta aplikačních přehledů: Enable-InstrumentationEngine

Tento článek popisuje rutinu, která je členem [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Popis

Povolí instrumentační modul nastavením některých klíčů registru.
Změny se projeví až po restartování služby IIS.

Instrumentační modul může doplnit data shromážděná sadami .NET SDK.
Shromažďuje události a zprávy, které popisují spuštění spravovaného procesu. Tyto události a zprávy zahrnují kódy výsledků závislostí, slovesa HTTP a [text příkazu SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Přístrojový motor povolte, pokud:
- Monitorování jste již povolili pomocí rutiny Povolit, ale nepovolili jste modul instrumentace.
- Ručně jste instrumentovali aplikaci pomocí sad SDK .NET a chcete shromažďovat další telemetrii.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci prostředí PowerShell s oprávněními správce.

> [!NOTE] 
> - Tato rutina vyžaduje, abyste si přečetli a přijali naše licenční a prohlášení o zásadách ochrany osobních údajů.
> - Přístrojový stroj přidává další režii a je ve výchozím nastavení vypnutý.

## <a name="examples"></a>Příklady

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parametry

### <a name="-acceptlicense"></a>-AcceptLicense
**Volitelné.** Pomocí tohoto přepínače můžete přijmout licenci a prohlášení o zásadách ochrany osobních údajů v bezhlavých instalacích.

### <a name="-verbose"></a>-Podrobné
**Společný parametr.** Tento přepínač použijte k výstupu podrobných protokolů.

## <a name="output"></a>Výstup


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Příklad výstupu z úspěšného povolení přístrojového stroje

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Další kroky

  Zobrazení telemetrických dat:
 - [Prozkoumejte metriky](../../azure-monitor/app/metrics-explorer.md) a sledujte výkon a využití.
- [Hledat události a protokoly](../../azure-monitor/app/diagnostic-search.md) diagnostikovat problémy.
- Pro pokročilejší dotazy používejte [analýzy.](../../azure-monitor/app/analytics.md)
- [Vytvořte řídicí panely](../../azure-monitor/app/overview-dashboard.md).
 
 Přidání další telemetrie:
 - [Vytvoření webových testů](monitor-web-app-availability.md) a ověření, jestli web zůstává živý.
- [Přidejte telemetrickou službu webového klienta,](../../azure-monitor/app/javascript.md) chcete-li zobrazit výjimky z kódu webové stránky a povolit volání trasování.
- [Přidejte do kódu sadou SDK application insights,](../../azure-monitor/app/asp-net.md) abyste mohli vkládat volání trasování a protokolování.
 
 S agentem Application Insights toho zvládnete víc:
 - Použijte náš průvodce [k řešení potíží s](status-monitor-v2-troubleshoot.md) agentem Application Insights.
 - [Získejte config](status-monitor-v2-api-get-config.md) a ověřte, zda byla vaše nastavení zaznamenána správně.
 - [Získejte stav](status-monitor-v2-api-get-status.md) pro kontrolu monitorování.
