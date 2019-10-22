---
title: Získejte více z Azure Application Insights | Microsoft Docs
description: Až začnete s Application Insights, tady je přehled funkcí, které můžete prozkoumat.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 02/03/2017
ms.openlocfilehash: 8c51745c43ced8ad3031a6a01096261ef72b33fc
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678358"
---
# <a name="more-telemetry-from-application-insights"></a>Další telemetrie z Application Insights
Po [přidání Application Insights do kódu ASP.NET](../../azure-monitor/app/asp-net.md)je k dispozici několik věcí, které můžete využít k získání ještě větší telemetrie. 

| Akce | Co získáváte|
|---|---|
|(Servery IIS) [Nainstalujte monitorování stavu](https://go.microsoft.com/fwlink/?LinkId=506648) na každém serverovém počítači.<br/>(Azure Web Apps) V Ovládacích panelech Azure pro webovou aplikaci otevřete okno Application Insights.| [**Čítače výkonu**](../../azure-monitor/app/performance-counters.md)<br/>[**Výjimky**](asp-net-exceptions.md) – podrobné trasování zásobníku<br/>[**Závislosti**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Přidat fragment kódu JavaScriptu na vaše webové stránky](../../azure-monitor/app/javascript.md)|[Výkon stránky](../../azure-monitor/app/usage-overview.md), výjimky prohlížeče, výkon AJAX. Vlastní telemetrie na straně klienta.|
|[Vytvořit webové testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md)|Získání výstrah v případě, že váš web nebude k dispozici|
|Ujistěte se, že nástroj MSBuild generuje [BuildInfo. config.](https://msdn.microsoft.com/library/dn449058.aspx)|[Poznámky k sestavení v grafech metrik](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Zápis vlastních událostí a metrik](../../azure-monitor/app/api-custom-events-metrics.md)|Počet obchodních událostí a metrik, sledování podrobného využití a další.|
|[Profilování živého webu](https://aka.ms/AIProfilerPreview)|Podrobné časování funkcí z živé webové aplikace|






