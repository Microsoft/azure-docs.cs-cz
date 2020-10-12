---
title: 'Application Insights: jazyky, platformy a integrace | Microsoft Docs'
description: Jazyky, platformy a integrace dostupné pro Application Insights
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: d3bb1473d94db04d1dedf2b09e19b83197cc6e0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88225100"
---
# <a name="supported-languages"></a>Podporované jazyky

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-get-started.md)
* [JavaScript](./javascript.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Podporované platformy a architektury

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentace pro už nasazené aplikace (bez kódu, na základě agentů)
* [Virtuální počítače Azure a Azure Virtual Machine Scale Sets](./azure-vm-vmss-apps.md)
* [Azure App Service](./azure-web-apps.md)
* [ASP.NET – pro aplikace, které jsou již nasazeny](./monitor-performance-live-website-now.md)
* [Cloud Services Azure](./cloudservices.md), včetně webových rolí a rolí pracovních procesů
* [Azure Functions](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>Instrumentace prostřednictvím kódu (sady SDK)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Android](../learn/mobile-center-quickstart.md) (App Center)
* [iOS](../learn/mobile-center-quickstart.md) (App Center)
* [Java EE](./java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](./opencensus-python.md)
* [Univerzální aplikace pro Windows](../learn/mobile-center-quickstart.md) (App Center)
* [Aplikace pracovní plochy Windows, služby a pracovní role](./windows-desktop.md)
* [React](./javascript-react-plugin.md)
* [React Native](./javascript-react-native-plugin.md)

## <a name="logging-frameworks"></a>Protokolování rozhraní
* [ILogger](./ilogger.md)
* [Log4Net, NLog nebo System.Diagnostics.Trace](./asp-net-trace-logs.md)
* [Java, Log4J nebo Logback](./java-trace-logs.md)
* [Modul plug-in pro LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>Export a analýza dat
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](./export-power-bi.md)

## <a name="unsupported-sdks"></a>Nepodporované sady SDK
Upozorňujeme, že existuje několik dalších sad SDK podporovaných komunitou. Azure Monitor však poskytuje podporu pouze při použití podporovaných sad SDK uvedených na této stránce. Neustále vyhodnotí příležitosti k rozšíření naší podpory pro jiné jazyky, proto na stránce [oznámení GitHubu](https://github.com/microsoft/ApplicationInsights-Announcements/issues) získáte nejnovější novinky SDK. 

