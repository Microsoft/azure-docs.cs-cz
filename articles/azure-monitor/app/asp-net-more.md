---
title: Lépe využívat Azure Application Insights | Dokumentace Microsoftu
description: Po zahájení práce s Application Insights, zde je uveden seznam funkcí, které můžete prozkoumat.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: 7580089782eb9330d5b533588265d156213f397f
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000576"
---
# <a name="more-telemetry-from-application-insights"></a>Další telemetrická data ze služby Application Insights
Až budete mít [přidat Application Insights do kódu ASP.NET](../../azure-monitor/app/asp-net.md), existuje několik věcí, které můžete udělat, abyste získali další telemetrické údaje. 

| Akce | Co získáte|
|---|---|
|(Servery služby IIS) [Nainstalujte monitorování stavu](https://go.microsoft.com/fwlink/?LinkId=506648) na každý počítač se serverem.<br/>(Webové aplikace azure) V Azure ovládací panely pro webovou aplikaci otevřete okno Application Insights.| [**Čítače výkonu**](../../azure-monitor/app/performance-counters.md)<br/>[**Výjimky** ](asp-net-exceptions.md) – podrobné trasování zásobníku<br/>[**Závislosti**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Přidejte javascriptový fragment kódu do webových stránek](../../azure-monitor/app/javascript.md)|[Stránka výkonu](../../application-insights/app-insights-usage-overview.md), výjimky prohlížeče, výkonu aplikace AJAX. Vlastní telemetrii na straně klienta.|
|[Vytvořit testy dostupnosti webu](../../azure-monitor/app/monitor-web-app-availability.md)|Dostávejte upozornění, pokud web přestane být k dispozici|
|[Zajištění buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) vygenerované nástrojem MSBuild|[Vytváření poznámek v grafy metrik](https://blogs.msdn.microsoft.com/visualstudioalm/2013/11/14/implementing-deployment-markers-in-application-insights/)
|[Napsat vlastní události a metriky](../../azure-monitor/app/api-custom-events-metrics.md)|Počet obchodní události a metriky, sledovat podrobné informace o využití a další.|
|[Profil živého webu](https://aka.ms/AIProfilerPreview)|Podrobné funkce časování z vaší živé webové aplikace|






