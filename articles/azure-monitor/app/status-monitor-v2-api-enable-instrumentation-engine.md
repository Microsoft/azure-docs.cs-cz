---
title: Reference k rozhraní API agenta Azure Application Insights
description: Reference k rozhraní API agenta Application Insights. Enable-InstrumentationEngine. Monitorujte výkon webu bez nutnosti opětovného nasazení webu. Funguje s ASP.NET webovými aplikacemi hostovanými místně, na virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b3f298ac31cc584cd16553186359c87f69f27aad
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671349"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>Rozhraní API pro Application Insights agenta: Enable-InstrumentationEngine

Tento článek popisuje rutinu, která je členem [modulu PowerShellu AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Popis

Povolí modul instrumentace nastavením některých klíčů registru.
Restartujte službu IIS, aby se změny projevily.

Modul instrumentace může doplnit data shromažďovaná sadami .NET SDK.
Shromažďuje události a zprávy, které popisují spuštění spravovaného procesu. Mezi tyto události a zprávy patří kódy výsledků závislosti, příkazy HTTP a [text příkazu SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Modul instrumentace povolte, pokud:
- Již jste povolili monitorování pomocí rutiny Enable, ale nepovolili jste modul instrumentace.
- Aplikaci jste ručně nastavili pomocí sady .NET SDK a chtěli byste shromažďovat další telemetrii.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci PowerShellu s oprávněními správce.

> [!NOTE] 
> - Tato rutina vyžaduje, abyste zkontrolovali a přijali naše licence a prohlášení o zásadách ochrany osobních údajů.
> - Modul instrumentace přidává další režii a je ve výchozím nastavení vypnutý.

## <a name="examples"></a>Příklady

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parametry

### <a name="-acceptlicense"></a>-AcceptLicense
**Volitelné.** Pomocí tohoto přepínače přijměte licence a prohlášení o zásadách ochrany osobních údajů v bezobslužné instalaci.

### <a name="-verbose"></a>– Verbose
**Společný parametr** Pomocí tohoto přepínače můžete vyvýstupovat podrobné protokoly.

## <a name="output"></a>Výstup


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Příklad výstupu úspěšného povolení modulu instrumentace

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
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
 - [Získáním konfigurace](status-monitor-v2-api-get-config.md) potvrďte, že vaše nastavení se správně nahrálo.
 - [Získejte stav](status-monitor-v2-api-get-status.md) pro kontrolu monitorování.
