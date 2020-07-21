---
title: Získejte více z Azure Application Insights | Microsoft Docs
description: Až začnete s Application Insights, tady je přehled funkcí, které můžete prozkoumat.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 6d30833840b62412ae03c761baacf54c939c9d33
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540022"
---
# <a name="more-telemetry-from-application-insights"></a>Další telemetrie z Application Insights
Po [přidání Application Insights do kódu ASP.NET](../../azure-monitor/app/asp-net.md)je k dispozici několik věcí, které můžete využít k získání ještě větší telemetrie. 

| Akce | Co získáte|
|---|---|
|(Servery IIS) [Nainstalujte monitorování stavu](https://go.microsoft.com/fwlink/?LinkId=506648) na každém serverovém počítači.<br/>(Azure Web Apps) V Ovládacích panelech Azure pro webovou aplikaci otevřete okno Application Insights.| [**Čítače výkonu**](../../azure-monitor/app/performance-counters.md)<br/>[**Výjimky**](asp-net-exceptions.md) – podrobné trasování zásobníku<br/>[**Závislosti**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Přidat fragment kódu JavaScriptu na vaše webové stránky](../../azure-monitor/app/javascript.md)|[Výkon stránky](../../azure-monitor/app/usage-overview.md), výjimky prohlížeče, výkon AJAX. Vlastní telemetrie na straně klienta.|
|[Vytvořit webové testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md)|Získání výstrah v případě, že váš web nebude k dispozici|
|[Zajistěte, aby byl buildinfo.config](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015) generovaný nástrojem MSBuild|[Poznámky k sestavení v grafech metrik](./annotations.md)
|[Zápis vlastních událostí a metrik](../../azure-monitor/app/api-custom-events-metrics.md)|Počet obchodních událostí a metrik, sledování podrobného využití a další.|
|[Profilování živého webu](https://aka.ms/AIProfilerPreview)|Podrobné časování funkcí z živé webové aplikace|
