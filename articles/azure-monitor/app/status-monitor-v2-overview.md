---
title: Přehled služby Azure Monitorování stavu v2 | Microsoft Docs
description: Přehled Monitorování stavu v2. Monitorujte výkon webu bez nutnosti opětovného nasazení webu. Funguje s ASP.NET webovými aplikacemi hostovanými místně, na virtuálních počítačích nebo v Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: tilee
ms.openlocfilehash: 7f045a95bcc9d5a61b26036e14e050a597347d1a
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286440"
---
# <a name="status-monitor-v2"></a>Monitorování stavu v2

Monitorování stavu v2 je modul prostředí PowerShell publikovaný do [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Nahrazuje [monitorování stavu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Modul poskytuje instrumentaci webových aplikací .NET, které jsou hostovány službou IIS, pomocí kódu.
Telemetrii se pošle do Azure Portal, kde můžete [monitorovat](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) svoji aplikaci.

## <a name="powershell-gallery"></a>Galerie prostředí PowerShell

Monitorování stavu v2 se nachází zde: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![Galerie prostředí PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Pokyny
- Začínáme s ukázkami stručných ukázek kódu najdete v [pokynech](status-monitor-v2-get-started.md) k zahájení práce.
- Projděte si [podrobné pokyny](status-monitor-v2-detailed-instructions.md) , jak v podrobně začít.

## <a name="powershell-api-reference"></a>Reference k rozhraní PowerShell API
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable – ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable – InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Spustit – ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Řešení potíží
- [Řešení potíží](status-monitor-v2-troubleshoot.md)
- [Známé problémy](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Časté otázky

- Podporuje Monitorování stavu v2 instalace proxy serveru?

  *Ano*. Existuje několik způsobů, jak stáhnout Monitorování stavu v2. Pokud má počítač přístup k Internetu, můžete k Galerie prostředí PowerShell připojit pomocí parametrů `-Proxy`.
Můžete také ručně stáhnout modul a buď ho nainstalovat do počítače, nebo ho použít přímo.
Každá z těchto možností je popsaná v [podrobných pokynech](status-monitor-v2-detailed-instructions.md).

- Podporuje Monitorování stavu v2 ASP.NET Core aplikace?

  *Ne*. Pokyny k povolení monitorování ASP.NET Corech aplikací najdete v tématu [Application Insights pro ASP.NET Core aplikace](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Pro ASP.NET Core aplikace není nutné instalovat StatusMonitor. Platí to i v případě, že je ASP.NET Core aplikace hostovaná ve službě IIS.

- Návody ověřit, zda bylo povolení úspěšné?

  - Pomocí rutiny [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) můžete ověřit, že je povolení úspěšné.
  - K rychlému určení, jestli vaše aplikace posílá telemetrii, doporučujeme použít [živé metriky](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) .

  - [Log Analytics](../log-query/get-started-portal.md) můžete použít také k vypsání všech cloudových rolí, které aktuálně odesílají telemetrii:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Další kroky

Zobrazení telemetrických dat:

* [Prozkoumejte metriky](../../azure-monitor/app/metrics-explorer.md) pro monitorování výkonu a využití.
* [Prohledejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) a Diagnostikujte problémy.
* K pokročilejším dotazům [použijte Analytics](../../azure-monitor/app/analytics.md) .
* [Vytváření řídicích panelů](../../azure-monitor/app/overview-dashboard.md).

Přidání další telemetrie:

* [Vytvářejte webové testy](monitor-web-app-availability.md) , abyste se ujistili, že vaše lokalita zůstane v provozu.
* [Přidejte telemetrii webového klienta](../../azure-monitor/app/javascript.md) pro zobrazení výjimek z kódu webové stránky a umožnění volání trasování.
* [Přidejte sadu Application Insights SDK do kódu](../../azure-monitor/app/asp-net.md) , abyste mohli vložit trasování a protokolování volání.

