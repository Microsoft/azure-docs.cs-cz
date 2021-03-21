---
title: Přehled služby Azure Application Insights Agent | Microsoft Docs
description: Přehled agenta Application Insights. Monitorujte výkon webu bez nutnosti opětovného nasazení webu. Funguje s ASP.NET webovými aplikacemi hostovanými místně, na virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 69f80856150e461c6edfafdf0aa89de77c4ab0fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100583803"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Nasazení agenta Azure Monitor Application Insights pro místní servery

> [!IMPORTANT]
> Tyto pokyny se doporučují pro nasazení Application Insights agenta v místním prostředí i mimo Azure. Tady je doporučený postup pro [nasazení virtuálních počítačů Azure a virtuálních počítačů ve službě Virtual Machine Scale set](./azure-vm-vmss-apps.md).

Agent Application Insights (dřív nazvaný Monitorování stavu v2) je modul prostředí PowerShell publikovaný do [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Nahrazuje [monitorování stavu](./monitor-performance-live-website-now.md).
Telemetrii se pošle do Azure Portal, kde můžete [monitorovat](./app-insights-overview.md) svoji aplikaci.

> [!NOTE]
> Modul aktuálně podporuje pouze nekódovatelné instrumentaci webových aplikací .NET, jejichž hostitelem je služba IIS. Pomocí sady SDK můžete instrumentovat aplikace ASP.NET Core, Java a Node.js.

## <a name="powershell-gallery"></a>Galerie prostředí PowerShell

Agent Application Insights se nachází zde: https://www.powershellgallery.com/packages/Az.ApplicationMonitor .

![Galerie prostředí PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Pokyny
- Začínáme s ukázkami stručných ukázek kódu najdete v [pokynech](status-monitor-v2-get-started.md) k zahájení práce.
- Projděte si [podrobné pokyny](status-monitor-v2-detailed-instructions.md) , jak v podrobně začít.

## <a name="powershell-api-reference"></a>Reference k rozhraní PowerShell API
- [Disable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#disable-applicationinsightsmonitoring)
- [Disable-InstrumentationEngine](./status-monitor-v2-api-reference.md#disable-instrumentationengine)
- [Enable – ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring)
- [Enable – InstrumentationEngine](./status-monitor-v2-api-reference.md#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringconfig)
- [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#set-applicationinsightsmonitoringconfig)
- [Spustit – ApplicationInsightsMonitoringTrace](./status-monitor-v2-api-reference.md#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Poradce při potížích
- [Řešení potíží](status-monitor-v2-troubleshoot.md)
- [Známé problémy](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Časté otázky

- Podporuje agent Application Insights instalaci proxy serveru?

  *Ano*. Existuje několik způsobů, jak stáhnout agenta Application Insights. Pokud má počítač přístup k Internetu, můžete k Galerie prostředí PowerShell připojit pomocí `-Proxy` parametrů.
Můžete také ručně stáhnout modul a buď ho nainstalovat do počítače, nebo ho použít přímo.
Každá z těchto možností je popsaná v [podrobných pokynech](status-monitor-v2-detailed-instructions.md).

- Podporuje Monitorování stavu v2 ASP.NET Core aplikace?

  *Ne*. Pokyny k povolení monitorování ASP.NET Corech aplikací najdete v tématu [Application Insights pro ASP.NET Core aplikace](./asp-net-core.md). Pro ASP.NET Core aplikace není nutné instalovat StatusMonitor. Platí to i v případě, že je ASP.NET Core aplikace hostovaná ve službě IIS.

- Návody ověřit, zda bylo povolení úspěšné?

  - Pomocí rutiny [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus) můžete ověřit, že je povolení úspěšné.
  - K rychlému určení, jestli vaše aplikace posílá telemetrii, doporučujeme použít [živé metriky](./live-stream.md) .

  - [Log Analytics](../logs/log-analytics-tutorial.md) můžete použít také k vypsání všech cloudových rolí, které aktuálně odesílají telemetrii:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Další kroky

Zobrazení telemetrických dat:

* [Prozkoumejte metriky](../essentials/metrics-charts.md) pro monitorování výkonu a využití.
* [Prohledejte události a protokoly](./diagnostic-search.md) a Diagnostikujte problémy.
* K pokročilejším dotazům [použijte Analytics](../logs/log-query-overview.md) .
* [Vytváření řídicích panelů](./overview-dashboard.md).

Přidání další telemetrie:

* [Vytvoření webových testů](monitor-web-app-availability.md) a ověření, jestli web zůstává živý.
* [Přidejte telemetrii webového klienta](./javascript.md) pro zobrazení výjimek z kódu webové stránky a umožnění volání trasování.
* [Přidejte sadu Application Insights SDK do kódu](./asp-net.md) , abyste mohli vložit trasování a protokolování volání.