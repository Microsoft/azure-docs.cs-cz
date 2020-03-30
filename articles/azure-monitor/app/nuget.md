---
title: Balíčky NuGet pro Azure Monitor Application Insights
description: Azure Monitor Application Insights NuGet balíčky seznamy pro ASP.NET, ASP.NET jádro, Python
ms.topic: reference
ms.date: 10/16/2018
ms.openlocfilehash: 5675c0fad61f1b75aa7e93db02246eed43967ec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669994"
---
# <a name="application-insights-nuget-packages"></a>Balíčky NuGet pro aplikační přehledy

Níže je aktuální seznam stabilní verze NuGet balíčky pro application insights.

## <a name="common-packages-for-aspnet"></a>Běžné balíčky pro ASP.NET

| Název balíčku | Stabilní verze | Popis | Stáhnout |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.12.0 | Poskytuje základní funkce pro přenos všech typů telemetrie Application Insights a je závislým balíčkem pro všechny ostatní balíčky Application Insights | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Soubor Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Povolí zachycení volání metody. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Kolekce závislostí přehledů aplikací pro aplikace .NET. Toto je závislý balíček pro balíčky specifické pro platformu Application Insights a poskytuje automatické shromažďování telemetrie závislostí. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Kolekce čítačů výkonu přehledů aplikací umožňuje odesílat data shromážděná čítači výkonu do přehledů aplikací. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.12.0 | Přehledy aplikací pro webové aplikace .NET | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Application Insights Balíček Windows Server NuGet poskytuje automatickou kolekci telemetrie přehledů aplikací pro aplikace .NET. Tento balíček lze použít jako závislý balíček pro balíčky specifické pro platformu Application Insights nebo jako samostatný balíček pro aplikace .NET, které nejsou pokryty balíčky specifické pro platformu (například pro role pracovních míst .NET). | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryKanál | 2.12.0 | Poskytuje telemetrický kanál pro soubor Application Insights windows server sdk, který zachová telemetrii v offline scénářích. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Běžné balíčky pro ASP.NET Core

| Název balíčku | Stabilní verze | Popis | Stáhnout |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Přehledy aplikací pro ASP.NET základní webové aplikace. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.12.0 | Tento balíček poskytuje základní funkce pro přenos všech typů telemetrie Application Insights a je závislým balíčkem pro všechny ostatní balíčky Application Insights | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Kolekce závislostí přehledů aplikací pro aplikace .NET. Toto je závislý balíček pro balíčky specifické pro platformu Application Insights a poskytuje automatické shromažďování telemetrie závislostí. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Kolekce čítačů výkonu přehledů aplikací umožňuje odesílat data shromážděná čítači výkonu do přehledů aplikací. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Application Insights Balíček Windows Server NuGet poskytuje automatickou kolekci telemetrie přehledů aplikací pro aplikace .NET. Tento balíček lze použít jako závislý balíček pro balíčky specifické pro platformu Application Insights nebo jako samostatný balíček pro aplikace .NET, které nejsou pokryty balíčky specifické pro platformu (například pro role pracovních míst .NET). | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryKanál | 2.12.0 | Poskytuje telemetrický kanál pro soubor Application Insights windows server sdk, který zachová telemetrii v offline scénářích. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Běžné balíčky pro Python pomocí OpenCensus
| Název balíčku | Stabilní verze | Popis | Stáhnout |
|-------------------------------|-----------------------|------------|----|
| opencensus-ext-azure | 1.0.0 | Přehledy aplikací pro aplikace v Pythonu v rámci Azure Monitor přes OpenCensus. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-ext-django | 0.7.2 | Tento balíček poskytuje integraci s knihovnou [Django](https://pypi.org/project/django/) v Pythonu. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-django/) |
| otevřená sčítací-ext-baňka | 0.7.3 | Tento balíček poskytuje integraci s knihovnou [baňek Pythonu.](https://pypi.org/project/flask/) | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-ext-httplib | 0.7.2 | Tento balíček poskytuje integraci s knihovnou [Pythonhttp.client](https://docs.python.org/3/library/http.client.html) pro Python3 a [httplib](https://docs.python.org/2/library/httplib.html) pro Python2. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-httplib/) |
| otevřená sčítání lidu-ext-protokolování | 0.1.0 | Tento balíček obohacuje záznamy protokolu o data trasování. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus-ext-mysql | 0.1.2 | Tento balíček poskytuje integraci s knihovnou Python [mysql-connector.](https://pypi.org/project/mysql-connector/) | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus-ext-postgresql | 0.1.2 | Tento balíček poskytuje integraci s knihovnou Python [psycopg2.](https://pypi.org/project/psycopg2/) | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus-ext-pymongo | 0.7.1 | Tento balíček poskytuje integraci s knihovnou [pymongo](https://pypi.org/project/pymongo/) Pythonu. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-ext-pymysql | 0.1.2 | Tento balíček poskytuje integraci s knihovnou Python [PyMySQL.](https://pypi.org/project/PyMySQL/) | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus-ext-pyramida | 0.7.1 | Tento balíček poskytuje integraci s [pyramidovou](https://pypi.org/project/pyramid/) knihovnou Pythonu. | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-pyramid/) |
| otevřené sčítání lidu-ext-žádosti | 0.7.2 | Tento balíček poskytuje integraci s knihovnou [požadavků Pythonu.](https://pypi.org/project/requests/) | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-ext-sqlalchemy | 0.1.2 | Tento balíček poskytuje integraci s knihovnou Python [SQLAlchemy.](https://pypi.org/project/SQLAlchemy/) | [Stáhnout balíček](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Posluchači/kolektory/elektory

| Název balíčku | Stabilní verze | Popis | Stáhnout |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Umožňuje předávání událostí z DiagnosticSource do Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener umožňuje odesílání dat z událostí EventSource do Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector umožňuje odesílání dat z trasování událostí pro Windows (ETW) do Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Vlastní TraceListener umožňuje odesílat zprávy protokolu trasování do Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Vlastní appender umožňuje odesílat zprávy protokolu Log4Net do Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  vlastní cíl, který umožňuje odesílat zprávy protokolu NLog do Application Insights. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Monitoruje výjimky ve vaší aplikaci a automaticky shromažďuje snímky pro offline analýzu. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Název balíčku | Stabilní verze | Popis | Stáhnout |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Tento balíček poskytuje automatické dekorace telemetrie s kontextem service fabric aplikace je spuštěna v. Nepoužívejte tento NuGet pro nativní aplikace Service Fabric. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Nativní | 2.2.0 | Modul Application Insights pro aplikace service fabric. Tento NuGet použijte pouze pro nativní aplikace Service Fabric. Pro aplikace spuštěné v kontejnerech použijte balíček Microsoft.ApplicationInsights.ServiceFabric. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Sledování stavu

| Název balíčku | Stabilní verze | Popis | Stáhnout |
|-------------------------------|-----------------------|------------|----|
| Soubor Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Umožňuje sběr dat za běhu pro aplikace x64 | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Soubor Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Umožňuje sběr dat za běhu pro aplikace x86. | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Tyto balíčky tvoří součást základní funkce monitorování za běhu v [programu Sledování stavu](../../azure-monitor/app/monitor-performance-live-website-now.md). Tyto balíčky nemusíte stahovat přímo, stačí použít instalační program sledování stavu. Pokud chcete pochopit více o tom, jak tyto balíčky fungují pod kapotou, tento [blogový příspěvek](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) od jednoho z našich vývojářů je dobrý začátek.

## <a name="additional-packages"></a>Další balíčky

| Název balíčku | Stabilní verze | Popis | Stáhnout |
|-------------------------------|-----------------------|------------|----|
| Weby Microsoft.ApplicationInsights.Azure | 2.6.5 | Toto rozšíření umožňuje monitorování Přehledy aplikací ve službě Azure App Service. Sada SDK verze 2.6.1. Instrukce: Přidejte nastavení aplikace "APPINSIGHTS_INSTRUMENTATIONKEY" s ikey a restartujte webovou aplikaci, aby se projevila.| [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Tento balíček obsahuje soubory potřebné pro vkládání bezkódových aplikačních přehledů | [Stáhnout balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Další kroky

- Monitor [ASP.NET jádro](../../azure-monitor/app/asp-net-core.md).
- Profil ASP.NET webových [aplikací core Azure Linux](../../azure-monitor/app/profiler-aspnetcore-linux.md).
- Ladění ASP.NET [snímků](../../azure-monitor/app/snapshot-debugger.md).
