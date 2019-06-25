---
title: Přehled služby Azure Monitor stavu v2 | Dokumentace Microsoftu
description: Přehled monitorování stavu v2. Sledování výkonu webu bez opětovného nasazení webu. Funguje s webové aplikace ASP.NET hostované místně, na virtuálních počítačích nebo v Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 2126408222433e6339723dc2da0d2611bb234fe8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734166"
---
# <a name="status-monitor-v2"></a>Monitorování stavu v2

Stav monitorování v2 je publikován v modulu Powershellu [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Nahradí [monitorování stavu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Tento modul obsahuje bez použití kódu instrumentace webových aplikací .NET hostovaných službou IIS.
Se odesílají telemetrická data na portál Azure Portal, kde můžete [monitorování](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) vaší aplikace.

> [!IMPORTANT]
> Stav monitorování v2 je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvu o úrovni služeb, a to nedoporučujeme pro produkční úlohy. Některé funkce nemusí být podporované a některé můžou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="powershell-gallery"></a>Galerie prostředí PowerShell

Galerie prostředí PowerShell je umístěná tady: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.


## <a name="instructions"></a>Pokyny
- Najdete v článku [pokyny Začínáme](status-monitor-v2-get-started.md) zobrazíte spuštění s ukázkami kódu stručné.
- Zobrazit [podrobné pokyny](status-monitor-v2-detailed-instructions.md) podrobné informace o tom, jak začít pracovat.

## <a name="powershell-api-reference"></a>Reference k rozhraní API prostředí PowerShell
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Povolit InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>Řešení potíží
- [Odstraňování potíží](status-monitor-v2-troubleshoot.md)
- [Známé problémy](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Nejčastější dotazy

- Monitorování stavu v2 podporuje instalace proxy?

  *Ano*. Stáhnout monitorování stavu v2 několika způsoby. Pokud má počítač přístup k Internetu, je možné připojit v galerii prostředí PowerShell pomocí `-Proxy` parametry.
Můžete také ručně stáhnout modul a buď ji nainstalujte na počítači nebo použijte přímo.
Každá z těchto možností podle [podrobné pokyny](status-monitor-v2-detailed-instructions.md).
  
- Jak můžu ověřit, zda bylo úspěšně povolení?

   Neexistuje žádné rutiny k ověření, ale bylo úspěšné.
Doporučujeme použít [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) a rychle tak určit Pokud vaše aplikace odesílá telemetrická data.

   Můžete také použít [Log Analytics](../log-query/get-started-portal.md) vypsat všechny role v cloudových procesech právě odesílá telemetrická data:
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>Další postup

Zobrazení telemetrických dat:

* [Zkoumání metrik](../../azure-monitor/app/metrics-explorer.md) pro monitorování výkonu a využití.
* [Prohledávejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) k diagnostice problémů.
* [Použití analýzy](../../azure-monitor/app/analytics.md) pro pokročilejší dotazy.
* [Vytváření řídicích panelů](../../azure-monitor/app/overview-dashboard.md).

Přidání další telemetrie:

* [Vytvářejte webové testy](monitor-web-app-availability.md) k Ujistěte se, že web zůstává živý.
* [Přidání telemetrie webového klienta](../../azure-monitor/app/javascript.md) pro zobrazení výjimek z kódu webové stránky a povolit trasování volání.
* [Přidejte Application Insights SDK do kódu](../../azure-monitor/app/asp-net.md) abyste mohli vložit trasování a protokolování volání.

