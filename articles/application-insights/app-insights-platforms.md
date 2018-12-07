---
title: 'Application Insights: jazyky, platformy a integrace | Dokumentace Microsoftu'
description: Jazyky, platformy a integrace, které jsou k dispozici pro službu Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/01/2016
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 9f7f7731490467ff32a72507239bfeedc794f2f2
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000540"
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Developer Analytics: jazyky, platformy a integrace
Tyto položky jsou implementace [Application Insights](app-insights-overview.md), o kterých jsme se dozvěděli. Zahrnují také některé, které dodávají třetí strany.

## <a name="languages---officially-supported-by-application-insights-team"></a>Jazyky – oficiálně podporované týmem služby Application Insights
* [C#|VB (.NET)](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Webové stránky JavaScript](app-insights-javascript.md)
* [Node.js](app-insights-nodejs.md)

## <a name="languages---community-supported"></a>Jazyky – podporované komunitou
* [F#](https://safe-stack.github.io/docs/template-azure-ai/)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Cokoliv jiného](#projects)

## <a name="platforms-and-frameworks"></a>Platformy a rozhraní
* [ASP.NET](app-insights-asp-net.md)
* [ASP.NET – pro aplikace, které jsou již nasazeny](app-insights-monitor-performance-live-website-now.md)
* [Jádro ASP.NET](app-insights-asp-net-core.md)
* [Android](app-insights-mobile-center-quickstart.md) (App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
* [Angular](https://github.com/MarkPieszak/angular-application-insights)
* [Azure Web Apps](app-insights-azure-web-apps.md)
* [Azure Cloud Services](app-insights-cloudservices.md)&#151; včetně webových a pracovních rolí
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](app-insights-docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](app-insights-mobile-center-quickstart.md) (App Center)
* [Ionic](https://github.com/SoftwarePioniere/ionic-application-insights)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (App Center)
* [J2EE](app-insights-java-get-started.md)
* [J2EE – pro aplikace, které jsou již nasazeny](app-insights-java-live.md)
* [Node.js](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [Stack SAFE](https://safe-stack.github.io/docs/template-azure-ai/)
* [Spring](https://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Univerzální aplikace pro Windows](app-insights-mobile-center-quickstart.md) (App Center)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Aplikace pracovní plochy Windows, služby a pracovní role](app-insights-windows-desktop.md)
* [Cokoliv jiného](#projects)

## <a name="logging-frameworks"></a>Protokolování rozhraní
* [Log4Net, NLog nebo System.Diagnostics.Trace](app-insights-asp-net-trace-logs.md)
* [Java, Log4J nebo Logback](app-insights-java-trace-logs.md)
* [Sémantické protokolování (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) – integruje se [sémantickým blokem protokolování aplikace](https://msdn.microsoft.com/library/dn440729.aspx)
* [Cloudové zátěžové testování](https://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [Modul plug-in pro LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)
* [Logrus](https://github.com/jjcollinge/logrus-appinsights)

## <a name="content-management-systems"></a>Systémy správy obsahu
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](app-insights-sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Export a analýza dat
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](app-insights-export-power-bi.md)

## <a name="projects"></a> Sestavte si vlastní sadu SDK
Pokud ještě není SDK k dispozici pro váš jazyk nebo platformu, možná si chcete jednu vytvořit? Prohlédněte si kód existující sady SDK, které jsou uvedeny v [Application Insights SDK projektu na Githubu](https://github.com/Microsoft/AppInsights-Home).
