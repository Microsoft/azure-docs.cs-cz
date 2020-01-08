---
title: Balíčky NuGet pro Azure Monitor Application Insights
description: Azure Monitor Application Insights seznamy balíčků NuGet pro ASP.NET, ASP.NET Core, Python
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/16/2018
ms.openlocfilehash: 0bac42242bc08fcc07bba12847a4aeb79424dc78
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406569"
---
# <a name="application-insights-nuget-packages"></a>Application Insights balíčky NuGet

Níže je uvedený aktuální seznam stabilních balíčků NuGet vydaných verzí pro Application Insights.

## <a name="common-packages-for-aspnet"></a>Běžné balíčky pro ASP.NET

| Název balíčku | Stabilní verze | Popis | Stáhnout |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.12.0 | Poskytuje základní funkce pro přenos všech typů Telemetrie Application Insights a jedná se o závislý balíček pro všechny ostatní balíčky Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Povoluje zachycení volání metod. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Application Insights kolektor závislostí pro aplikace .NET. Toto je závislý balíček pro Application Insights balíčky specifické pro platformu a poskytuje automatické shromažďování telemetrie závislostí. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Kolektor čítačů výkonu Application Insights umožňuje odesílat data shromážděná čítači výkonu do Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.12.0 | Application Insights pro webové aplikace .NET | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Application Insights balíček NuGet pro Windows Server poskytuje automatickou shromažďování telemetrie Application Insights pro aplikace .NET. Tento balíček se dá použít jako závislý balíček pro Application Insights balíčky pro konkrétní platformu nebo jako samostatný balíček pro aplikace .NET, na které se nevztahují balíčky specifické pro danou platformu (například pro role pracovních procesů .NET). | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | Poskytuje kanál telemetrie pro Application Insights Windows Server SDK, který bude zachovávat telemetrii v offline scénářích. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Běžné balíčky pro ASP.NET Core

| Název balíčku | Stabilní verze | Popis | Stáhnout |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights pro ASP.NET Core webové aplikace | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.12.0 | Tento balíček poskytuje základní funkce pro přenos všech typů Telemetrie Application Insights a jedná se o závislý balíček pro všechny ostatní balíčky Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Application Insights kolektor závislostí pro aplikace .NET. Toto je závislý balíček pro Application Insights balíčky specifické pro platformu a poskytuje automatické shromažďování telemetrie závislostí. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Kolektor čítačů výkonu Application Insights umožňuje odesílat data shromážděná čítači výkonu do Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Application Insights balíček NuGet pro Windows Server poskytuje automatickou shromažďování telemetrie Application Insights pro aplikace .NET. Tento balíček se dá použít jako závislý balíček pro Application Insights balíčky pro konkrétní platformu nebo jako samostatný balíček pro aplikace .NET, na které se nevztahují balíčky specifické pro danou platformu (například pro role pracovních procesů .NET). | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | Poskytuje kanál telemetrie pro Application Insights Windows Server SDK, který bude zachovávat telemetrii v offline scénářích. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Společné balíčky pro Python využívající OpenCensus
| Název balíčku | Stabilní verze | Popis | Stáhnout |
|-------------------------------|-----------------------|------------|----|
| opencensus – EXT – Azure | 1.0.0 | Application Insights pro aplikace Pythonu v části Azure Monitor přes OpenCensus. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-EXT-Django | 0.7.2 | Tento balíček poskytuje integraci s knihovnou [Django](https://pypi.org/project/django/) Pythonu. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-django/) |
| opencensus-EXT-baňka | 0.7.3 | Tento balíček poskytuje integraci s knihovnou [baněk](https://pypi.org/project/flask/) Pythonu. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-EXT-httplib | 0.7.2 | Tento balíček poskytuje integraci se službou Python [http. Client](https://docs.python.org/3/library/http.client.html) Library pro python3 a [httplib](https://docs.python.org/2/library/httplib.html) pro Python2. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-httplib/) |
| opencensus-EXT-protokolování | 0.1.0 | Tento balíček rozšiřuje záznamy protokolu s daty trasování. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus-EXT-MySQL | 0.1.2 | Tento balíček poskytuje integraci s knihovnou [konektoru Python MySQL](https://pypi.org/project/mysql-connector/) . | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus-EXT-PostgreSQL | 0.1.2 | Tento balíček poskytuje integraci s knihovnou [Psycopg2](https://pypi.org/project/psycopg2/) Pythonu. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus-EXT-pymongo | 0.7.1 | Tento balíček poskytuje integraci s knihovnou [Pymongo](https://pypi.org/project/pymongo/) Pythonu. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-EXT-pymysql | 0.1.2 | Tento balíček poskytuje integraci s knihovnou [PyMySQL](https://pypi.org/project/PyMySQL/) Pythonu. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus – EXT – jehlan | 0.7.1 | Tento balíček poskytuje integraci s knihovnou [pyramidy](https://pypi.org/project/pyramid/) Pythonu. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-pyramid/) |
| opencensus-EXT-požadavky | 0.7.2 | Tento balíček poskytuje integraci s knihovnou [žádostí](https://pypi.org/project/requests/) Pythonu. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-EXT-sqlalchemy | 0.1.2 | Tento balíček poskytuje integraci s knihovnou [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) Pythonu. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Naslouchací procesy/sběrače/připojovatelné objekty

| Název balíčku | Stabilní verze | Popis | Stáhnout |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Umožňuje předávání událostí z DiagnosticSource do Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener umožňuje odesílání dat z událostí EventSource do Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector umožňuje odesílání dat z trasování událostí pro Windows (ETW) do Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Vlastní TraceListener, který vám umožní odesílat zprávy protokolu trasování do Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Vlastní připojení, které umožňuje odesílat zprávy protokolu Log4Net do Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  vlastní cíl umožňující odesílání zpráv protokolu NLog do Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Monitoruje výjimky ve vaší aplikaci a automaticky shromažďuje snímky pro offline analýzu. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Název balíčku | Stabilní verze | Popis | Stáhnout |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Tento balíček poskytuje automatickou dekorace telemetrie s kontextem Service Fabric, ve kterém je aplikace spuštěná. Nepoužívejte tuto NuGet pro nativní aplikace Service Fabric. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Application Insights modul pro aplikace Service Fabric. Tuto NuGet použijte jenom pro nativní aplikace Service Fabric. Pro aplikace běžící v kontejnerech použijte balíček Microsoft. ApplicationInsights. ServiceFabric. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Monitorování stavu

| Název balíčku | Stabilní verze | Popis | Stáhnout |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Povoluje shromažďování dat za běhu pro aplikace x64. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Povoluje shromažďování dat za běhu pro aplikace x86. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Tyto balíčky tvoří součást základní funkce monitorování za běhu v [monitorování stavu](../../azure-monitor/app/monitor-performance-live-website-now.md). Tyto balíčky nemusíte stahovat přímo, stačí použít instalační program Monitorování stavu. Pokud chcete získat další informace o tom, jak tyto balíčky v digestoři pracují, je dobrým začátkem tento [Blogový příspěvek](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) od jednoho z našich vývojářů.

## <a name="additional-packages"></a>Další balíčky

| Název balíčku | Stabilní verze | Popis | Stáhnout |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | Toto rozšíření umožňuje Application Insights monitorování Azure App Service. Sada SDK verze 2.6.1. Pokyny: přidejte do ikey nastavení aplikace APPINSIGHTS_INSTRUMENTATIONKEY a restartujte WebApp, aby se projevily.| [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Tento balíček obsahuje soubory, které jsou nutné pro vkládání kódu Application Insights | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Další kroky

- [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)monitorování.
- Profil ASP.NET Core [webové aplikace Azure Linux](../../azure-monitor/app/profiler-aspnetcore-linux.md).
- Ladění [snímků](../../azure-monitor/app/snapshot-debugger.md)ASP.NET
