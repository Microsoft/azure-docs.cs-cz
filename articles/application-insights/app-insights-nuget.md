---
title: Azure Application Insights – automatického sběru závislost | Dokumentace Microsoftu
description: Application Insights automaticky shromažďovat a vizualizace závislostí
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/16/2018
ms.author: mbullwin
ms.openlocfilehash: adf055983ad4247101401b907024328607a19bfe
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017136"
---
# <a name="application-insights-nuget-packages"></a>Balíčky Insights NuGet aplikací

Níže je uvedený aktuální seznam stabilní verzi balíčků NuGet pro Application Insights.

## <a name="common-packages-for-aspnet"></a>Běžné balíčky pro technologii ASP.NET

| Název balíčku | Stabilní verze | Popis | Ke stažení |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.8.0 | Poskytuje základní funkce pro předávání všechny typy Telemetrie Application Insights a je závislý balíček pro jiné balíčky Application Insights | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Povolí zachycení volání metod | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Application Insights závislost Collector pro aplikace .NET. Toto je závislý balíček Application Insights balíčků specifických pro platformu a poskytuje Automatický sběr telemetrických závislostí. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Application Insights výkon čítače Collector umožňuje odesílání dat shromažďovaných čítačů výkonu do Application Insights. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.8.0 | Webové aplikace Application Insights pro .NET | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Balíček aplikace NuGet Insights systému Windows Server poskytuje Automatický sběr hodnot telemetrie application insights pro aplikace .NET. Tento balíček je možné jako závislý balíček Application Insights balíčků specifických pro platformu, nebo jako samostatného balíčku pro aplikace .NET, které nejsou pokryty všemi balíčků specifických pro platformy (jako u rolí pracovního procesu .NET). | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Poskytuje kanál telemetrická data do systému Windows Server sadu SDK Application Insights, který se zachová telemetrických dat v případě offline scénářů. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Běžné balíčky pro ASP.NET Core

| Název balíčku | Stabilní verze | Popis | Ke stažení |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Aplikace pro web Application Insights pro ASP.NET Core. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.8.0 | Tento balíček poskytuje základní funkce pro předávání všechny typy Telemetrie Application Insights a je závislý balíček pro jiné balíčky Application Insights | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Application Insights závislost Collector pro aplikace .NET. Toto je závislý balíček Application Insights balíčků specifických pro platformu a poskytuje Automatický sběr telemetrických závislostí. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Application Insights výkon čítače Collector umožňuje odesílání dat shromažďovaných čítačů výkonu do Application Insights. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Balíček aplikace NuGet Insights systému Windows Server poskytuje Automatický sběr hodnot telemetrie application insights pro aplikace .NET. Tento balíček je možné jako závislý balíček Application Insights balíčků specifických pro platformu, nebo jako samostatného balíčku pro aplikace .NET, které nejsou pokryty všemi balíčků specifických pro platformy (jako u rolí pracovního procesu .NET). | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Poskytuje kanál telemetrická data do systému Windows Server sadu SDK Application Insights, který se zachová telemetrických dat v případě offline scénářů. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="listenerscollectorsappenders"></a>Naslouchací procesy/kolekce/appenders

| Název balíčku | Stabilní verze | Popis | Ke stažení |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Umožňuje předávání událostí z DiagnosticSource do Application Insights. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener umožňuje odesílání dat do služby Application Insights z událostí EventSource. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector umožňuje odesílání dat do služby Application Insights z trasování událostí pro Windows (ETW). | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Vlastní TraceListener umožňuje posílat zprávy protokolu trasování do Application Insights. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Vlastní appenderu protokolovacího umožňuje odesílání zpráv protokolu Log4Net do Application Insights. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  vlastní cílové umožňuje posílat do Application Insights NLog zprávy protokolu. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Sleduje výjimky ve vaší aplikaci a automaticky shromažďuje snímky pro offline analýzu. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Název balíčku | Stabilní verze | Popis | Ke stažení |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Tento balíček poskytuje automatické dekorace telemetrických dat s kontextem service fabric aplikace běží v. Nepoužívejte tuto NuGet pro aplikace nativní Service Fabric. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Modul Application Insights pro aplikace service fabric. Používejte tento NuGet pouze pro aplikace nativní Service Fabric. U aplikací spouštěných v kontejnerech použijte Microsoft.ApplicationInsights.ServiceFabric balíčku. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Monitorování stavu

| Název balíčku | Stabilní verze | Popis | Ke stažení |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Umožňuje shromažďovat data modulu runtime pro x64 aplikací | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Umožňuje shromažďovat data modulu runtime pro x86 aplikací. | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Tyto balíčky, které tvoří součást základní funkce monitorování runtime v [monitorování stavu](../azure-monitor/app/monitor-performance-live-website-now.md). Není potřeba přímo stahovat tyto balíčky, použijte instalační program monitorování stavu. Pokud chcete se dozvědět více o tom, jak tyto balíčky pracovat pod pokličkou [blogový příspěvek](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) z jednoho z našich vývojářů je dobrý začátek.

## <a name="additional-packages"></a>Další balíčky

| Název balíčku | Stabilní verze | Popis | Ke stažení |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | Toto rozšíření umožňuje monitorování Application Insights do služby Azure App Service. Sada SDK verze 2.6.1. Pokyny: Přidat nastavení aplikace "APPINSIGHTS_INSTRUMENTATIONKEY" s svůj Instrumentační klíč a restartujte webové aplikace se projeví.| [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Tento balíček obsahuje soubory potřebné pro vkládání bez použití kódu Application Insights | [Stáhněte si balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Další postup

- Monitorování [ASP.NET Core](../azure-monitor/app/asp-net-core.md).
- Profil ASP.NET Core [webové aplikace Azure s Linuxem](../azure-monitor/app/profiler-aspnetcore-linux.md).
- Ladění technologie ASP.NET [snímky](../azure-monitor/app/snapshot-debugger.md).