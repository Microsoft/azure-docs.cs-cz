---
title: 'Application Insights: jazyky, platformy a integrace | Dokumenty společnosti Microsoft'
description: Jazyky, platformy a integrace dostupné pro application insights
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: cbda252fece638393ee7757dc165c4b009a0195e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136719"
---
# <a name="supported-languages"></a>Podporované jazyky

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Javascript](../../azure-monitor/app/javascript.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Podporované platformy a rámce

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentace pro již nasazené aplikace (bez kódu, na základě agenta)
* [Škálovací sady virtuálních zařízení Azure a virtuálních strojů Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET – pro aplikace, které jsou již nasazeny](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Cloudové služby Azure](../../azure-monitor/app/cloudservices.md), včetně webových i pracovních rolí
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Instrumentace prostřednictvím kódu (SDK)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.js](https://www.npmjs.com/package/applicationinsights)
* [Python](../../azure-monitor/app/opencensus-python.md)
* [Univerzální aplikace pro Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Aplikace pracovní plochy Windows, služby a pracovní role](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Protokolování rozhraní
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog nebo System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J nebo Logback](../../azure-monitor/app/java-trace-logs.md)
* [Modul plug-in pro LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Export a analýza dat
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Analýza streamu](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>Nepodporované sady SDK
Jsme si vědomi, že existuje několik dalších sad SDK podporovaných komunitou. Azure Monitor však poskytuje podporu pouze při použití podporovaných sad SDK uvedených na této stránce. Neustále vyhodnocujeme příležitosti k rozšíření naší podpory pro další jazyky, takže sledujte naši stránku [Oznámení GitHubu](https://github.com/microsoft/ApplicationInsights-Announcements/issues) a získejte nejnovější zprávy sady SDK. 
