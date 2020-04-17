---
title: Přehled agenta Azure Application Insights | Dokumenty společnosti Microsoft
description: Přehled agenta Application Insights. Sledujte výkon webových stránek bez opětovného nasazení webu. Funguje s ASP.NET webových aplikací hostovaných místně, ve virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 4042fc05f278915fe72bf8fc4e6afd69a5bbb4b9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537419"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Nasazení agenta Azure Monitor Application Insights pro místní servery

> [!IMPORTANT]
> Tyto pokyny se doporučují pro místní a neazure cloudová nasazení agenta Application Insights. Tady je doporučený přístup pro [nasazení virtuálních strojů Azure a škálovací sady virtuálních strojů](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps).

Agent Application Insights (dříve pojmenovaný Stavový monitor V2) je modul prostředí PowerShell publikovaný v [Galerii prostředí PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Nahrazuje [sledování stavu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Telemetrie se odesílá na portál Azure, kde můžete [sledovat](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) svou aplikaci.

> [!NOTE]
> Modul v současné době podporuje pouze bezcodeinstrumentaci webových aplikací .NET hostovaných se službou IIS. Sadou SDK můžete instrumentovat ASP.NET aplikací core, java a node.js.

## <a name="powershell-gallery"></a>Galerie prostředí PowerShell

Agent Application Insights se https://www.powershellgallery.com/packages/Az.ApplicationMonitornachází zde: .

![Galerie prostředí PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Pokyny
- Podívejte se na [pokyny začínáme](status-monitor-v2-get-started.md) začít s stručné ukázky kódu.
- Podívejte se na [podrobné pokyny](status-monitor-v2-detailed-instructions.md) pro podrobný ponor o tom, jak začít.

## <a name="powershell-api-reference"></a>Odkaz na rozhraní API prostředí PowerShell
- [Zakázat-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Funkce Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngineEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Spuštění aplikaceInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Řešení potíží
- [Odstraňování potíží](status-monitor-v2-troubleshoot.md)
- [Známé problémy](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Nejčastější dotazy

- Podporuje agent Application Insights instalace proxy serveru?

  *Ano.* Existuje několik způsobů, jak stáhnout Agent a insights aplikací. Pokud má váš počítač přístup k internetu, můžete se `-Proxy` k galerii prostředí PowerShell připojit pomocí parametrů.
Modul můžete také ručně stáhnout a nainstalovat do počítače nebo jej použít přímo.
Každá z těchto možností je popsána v [podrobných pokynech](status-monitor-v2-detailed-instructions.md).

- Podporuje sledování stavu v2 ASP.NET základních aplikací?

  *Ne*. Pokyny k povolení monitorování ASP.NET základních aplikací naleznete v tématu [Application Insights for ASP.NET Core applications](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Není třeba instalovat StatusMonitor pro aplikaci ASP.NET Core. To platí i v případě, ASP.NET základní aplikace je hostována ve službách IIS.

- Jak ověřím, že povolení proběhlo úspěšně?

  - Rutina [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) slouží k ověření, že povolení bylo úspěšné.
  - Doporučujeme použít [live metriky](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) rychle zjistit, jestli vaše aplikace odesílá telemetrická data.

  - Pomocí [analýzy protokolů](../log-query/get-started-portal.md) můžete také vypsat všechny cloudové role, které aktuálně odesílají telemetrii:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Další kroky

Zobrazení telemetrických dat:

* [Prozkoumejte metriky](../../azure-monitor/platform/metrics-charts.md) a sledujte výkon a využití.
* [Hledat události a protokoly](../../azure-monitor/app/diagnostic-search.md) diagnostikovat problémy.
* Pro pokročilejší dotazy [použijte službu Analytics.](../../azure-monitor/app/analytics.md)
* [Vytvořte řídicí panely](../../azure-monitor/app/overview-dashboard.md).

Přidání další telemetrie:

* [Vytvoření webových testů](monitor-web-app-availability.md) a ověření, jestli web zůstává živý.
* [Přidejte telemetrickou službu webového klienta,](../../azure-monitor/app/javascript.md) chcete-li zobrazit výjimky z kódu webové stránky a povolit volání trasování.
* [Přidejte do kódu sadou SDK application insights,](../../azure-monitor/app/asp-net.md) abyste mohli vkládat volání trasování a protokolování.

