---
title: Získejte více z Azure Application Insights | Microsoft Docs
description: Až začnete s Application Insights, tady je přehled funkcí, které můžete prozkoumat.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: c0be377636159dbb2bdb628af6a0bc9077942397
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321339"
---
# <a name="more-telemetry-from-application-insights"></a>Další telemetrie z Application Insights
Po [přidání Application Insights do kódu ASP.NET](./asp-net.md)je k dispozici několik věcí, které můžete využít k získání ještě větší telemetrie. 

| Akce | Co získáte|
|---|---|
|(Servery IIS) [Nainstalujte monitorování stavu](https://go.microsoft.com/fwlink/?LinkId=506648) na každém serverovém počítači.<br/>(Azure Web Apps) V Ovládacích panelech Azure pro webovou aplikaci otevřete okno Application Insights.| [**Čítače výkonu**](./performance-counters.md)<br/>[**Výjimky**](asp-net-exceptions.md) – podrobné trasování zásobníku<br/>[**Závislosti**](./asp-net-dependencies.md)|
|[Přidat fragment kódu JavaScriptu na vaše webové stránky](./javascript.md)|[Výkon stránky](./usage-overview.md), výjimky prohlížeče, výkon AJAX. Vlastní telemetrie na straně klienta.|
|[Vytvořit webové testy dostupnosti](./monitor-web-app-availability.md)|Získání výstrah v případě, že váš web nebude k dispozici|
|[Zajistěte, aby byl buildinfo.config](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015) generovaný nástrojem MSBuild|[Poznámky k sestavení v grafech metrik](./annotations.md)
|[Zápis vlastních událostí a metrik](./api-custom-events-metrics.md)|Počet obchodních událostí a metrik, sledování podrobného využití a další.|
|[Profilování živého webu](https://aka.ms/AIProfilerPreview)|Podrobné časování funkcí z živé webové aplikace|

