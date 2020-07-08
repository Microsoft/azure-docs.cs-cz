---
title: Přehled služby Azure Application Insights Agent | Microsoft Docs
description: Přehled agenta Application Insights. Monitorujte výkon webu bez nutnosti opětovného nasazení webu. Funguje s ASP.NET webovými aplikacemi hostovanými místně, na virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 626a8c96fdc247db174e2cbcd2e5c99cec43d2ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81770966"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Nasazení agenta Azure Monitor Application Insights pro místní servery

> [!IMPORTANT]
> Tyto pokyny se doporučují pro nasazení Application Insights agenta v místním prostředí i mimo Azure. Tady je doporučený postup pro [nasazení virtuálních počítačů Azure a virtuálních počítačů ve službě Virtual Machine Scale set](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps).

Agent Application Insights (dřív nazvaný Monitorování stavu v2) je modul prostředí PowerShell publikovaný do [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Nahrazuje [monitorování stavu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Telemetrii se pošle do Azure Portal, kde můžete [monitorovat](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) svoji aplikaci.

> [!NOTE]
> Modul aktuálně podporuje pouze nekódovatelné instrumentaci webových aplikací .NET, jejichž hostitelem je služba IIS. Pomocí sady SDK můžete instrumentovat aplikace ASP.NET Core, Java a Node.js.

## <a name="powershell-gallery"></a>Galerie prostředí PowerShell

Agent Application Insights se nachází zde: https://www.powershellgallery.com/packages/Az.ApplicationMonitor .

![Galerie prostředí PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Pokyny
- Začínáme s ukázkami stručných ukázek kódu najdete v [pokynech](status-monitor-v2-get-started.md) k zahájení práce.
- Projděte si [podrobné pokyny](status-monitor-v2-detailed-instructions.md) , jak v podrobně začít.

## <a name="powershell-api-reference"></a>Reference k rozhraní PowerShell API
- [Disable-ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-applicationinsightsmonitoring)
- [Disable-InstrumentationEngine](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-instrumentationengine)
- [Enable – ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring)
- [Enable – InstrumentationEngine](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringconfig)
- [Get-ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#set-applicationinsightsmonitoringconfig)
- [Spustit – ApplicationInsightsMonitoringTrace](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Řešení potíží
- [Řešení potíží](status-monitor-v2-troubleshoot.md)
- [Známé problémy](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Nejčastější dotazy

- Podporuje agent Application Insights instalaci proxy serveru?

  *Ano*. Existuje několik způsobů, jak stáhnout agenta Application Insights. Pokud má počítač přístup k Internetu, můžete k Galerie prostředí PowerShell připojit pomocí `-Proxy` parametrů.
Můžete také ručně stáhnout modul a buď ho nainstalovat do počítače, nebo ho použít přímo.
Každá z těchto možností je popsaná v [podrobných pokynech](status-monitor-v2-detailed-instructions.md).

- Podporuje Monitorování stavu v2 ASP.NET Core aplikace?

  *Ne*. Pokyny k povolení monitorování ASP.NET Corech aplikací najdete v tématu [Application Insights pro ASP.NET Core aplikace](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Pro ASP.NET Core aplikace není nutné instalovat StatusMonitor. Platí to i v případě, že je ASP.NET Core aplikace hostovaná ve službě IIS.

- Návody ověřit, zda bylo povolení úspěšné?

  - Pomocí rutiny [Get-ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus) můžete ověřit, že je povolení úspěšné.
  - K rychlému určení, jestli vaše aplikace posílá telemetrii, doporučujeme použít [živé metriky](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) .

  - [Log Analytics](../log-query/get-started-portal.md) můžete použít také k vypsání všech cloudových rolí, které aktuálně odesílají telemetrii:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Další kroky

Zobrazení telemetrických dat:

* [Prozkoumejte metriky](../../azure-monitor/platform/metrics-charts.md) pro monitorování výkonu a využití.
* [Prohledejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) a Diagnostikujte problémy.
* K pokročilejším dotazům [použijte Analytics](../../azure-monitor/app/analytics.md) .
* [Vytváření řídicích panelů](../../azure-monitor/app/overview-dashboard.md).

Přidání další telemetrie:

* [Vytvoření webových testů](monitor-web-app-availability.md) a ověření, jestli web zůstává živý.
* [Přidejte telemetrii webového klienta](../../azure-monitor/app/javascript.md) pro zobrazení výjimek z kódu webové stránky a umožnění volání trasování.
* [Přidejte sadu Application Insights SDK do kódu](../../azure-monitor/app/asp-net.md) , abyste mohli vložit trasování a protokolování volání.

