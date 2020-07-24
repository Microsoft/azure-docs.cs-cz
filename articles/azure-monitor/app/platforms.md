---
title: 'Application Insights: jazyky, platformy a integrace | Microsoft Docs'
description: Jazyky, platformy a integrace dostupné pro Application Insights
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: a5403162a2511862dd1c649dc273a35a550abaaf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87024278"
---
# <a name="supported-languages"></a>Podporované jazyky

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Podporované platformy a architektury

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentace pro už nasazené aplikace (bez kódu, na základě agentů)
* [Virtuální počítače Azure a Azure Virtual Machine Scale Sets](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET – pro aplikace, které jsou již nasazeny](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Cloud Services Azure](../../azure-monitor/app/cloudservices.md), včetně webových rolí a rolí pracovních procesů
* [Azure Functions](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>Instrumentace prostřednictvím kódu (sady SDK)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](../../azure-monitor/app/opencensus-python.md)
* [Univerzální aplikace pro Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Aplikace pracovní plochy Windows, služby a pracovní role](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Protokolování rozhraní
* [ILogger](./ilogger.md)
* [Log4Net, NLog nebo System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J nebo Logback](../../azure-monitor/app/java-trace-logs.md)
* [Modul plug-in pro LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>Export a analýza dat
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>Nepodporované sady SDK
Upozorňujeme, že existuje několik dalších sad SDK podporovaných komunitou. Azure Monitor však poskytuje podporu pouze při použití podporovaných sad SDK uvedených na této stránce. Neustále vyhodnotí příležitosti k rozšíření naší podpory pro jiné jazyky, proto na stránce [oznámení GitHubu](https://github.com/microsoft/ApplicationInsights-Announcements/issues) získáte nejnovější novinky SDK. 
