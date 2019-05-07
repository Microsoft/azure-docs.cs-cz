---
title: Přehled služby Azure Monitor stavu v2 | Dokumentace Microsoftu
description: Přehled monitorování stavu v2. Sledování výkonu webu bez opětovného nasazení webu. Funguje s místně hostovanými webovými aplikacemi v ASP.NET, na virtuálních počítačích nebo v Azure.
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
ms.openlocfilehash: 77c2cd9e0eac6717d91a73a6fc033dcaa2390444
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145031"
---
# <a name="status-monitor-v2"></a>Monitorování stavu v2

Stav monitorování v2 je modul Powershellu publikovaná [PowerShellGallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor) a je náhradou [monitorování stavu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now). Tento modul poskytuje méně kód instrumentace webové aplikace .NET hostované službou IIS.
Telemetrická data se odešlou na portál Azure Portal kde pak můžete [monitorování](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) vaší aplikace.

> [!IMPORTANT]
> Stav monitorování v2 je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="powershell-gallery"></a>Galerie prostředí PowerShell

https://www.powershellgallery.com/packages/Az.ApplicationMonitor


## <a name="instructions"></a>Pokyny
- Kontrola naše [pokyny Začínáme](status-monitor-v2-get-started.md) jak hned začít s ukázkami kódu stručné.
- Kontrola naše [podrobné pokyny](status-monitor-v2-detailed-instructions.md) podrobné informace o tom, jak začít pracovat.

## <a name="powershell-api-reference"></a>Reference k rozhraní API prostředí PowerShell
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Povolit InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>Řešení potíží
- [Řešení potíží](status-monitor-v2-troubleshoot.md)
- [Známé problémy](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Nejčastější dotazy

- Monitorování stavu v2 podporuje instalace proxy?

  **Ano**. Máte několik možností, které chcete stáhnout monitorování stavu v2. Pokud má počítač přístup k Internetu, můžete se připojit k pomocí Galerie prostředí PowerShell `-Proxy` parametry. Můžete také ručně stáhnout tento modul a buď ji nainstalujte na svém počítači nebo přímo pomocí modulu. Každá z těchto možností je popsaný v našich [podrobné pokyny](status-monitor-v2-detailed-instructions.md).
  
- Ověření povolení byla úspěšná?

   Pokud chcete ověřit že úspěšné této funkce pro kontrolu nemáme rutinu. Doporučujeme používat [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) rychle sledovat, pokud vaše aplikace je odeslání telemetrie.

## <a name="next-steps"></a>Další postup

Zobrazení telemetrických dat:

* [Zkoumání metrik](../../azure-monitor/app/metrics-explorer.md) pro monitorování výkonu a využití
* [Prohledávejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) k diagnostice problémů
* [Analýzy](../../azure-monitor/app/analytics.md) pro pokročilejší dotazy
* [Vytváření řídicích panelů](../../azure-monitor/app/app-insights-dashboards.md)

Přidání další telemetrie:

* [Vytvářejte webové testy](monitor-web-app-availability.md) k Ujistěte se, že web zůstává živý.
* [Přidání telemetrie webového klienta](../../azure-monitor/app/javascript.md) pro zobrazení výjimek z kódu webové stránky a umožnění vložení trasovacích volání.
* [Přidejte sadu Application Insights SDK do kódu](../../azure-monitor/app/asp-net.md) , mohli vložit trasování a protokolování volání

