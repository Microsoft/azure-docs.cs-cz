---
title: Získejte více z Přehledů aplikací Azure | Dokumenty společnosti Microsoft
description: Po zahájení používání přehledů aplikací najdete přehled funkcí, které můžete prozkoumat.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 3c4a9a736f34d4f7dbfeb004d0837f1f2efa55dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666020"
---
# <a name="more-telemetry-from-application-insights"></a>Další telemetrie z Application Insights
Po [přidání Application Insights do kódu ASP.NET](../../azure-monitor/app/asp-net.md), existuje několik věcí, které můžete udělat, abyste získali ještě více telemetrie. 

| Akce | Co získáte|
|---|---|
|(Servery Služby IIS) [Nainstalujte sledování stavu](https://go.microsoft.com/fwlink/?LinkId=506648) do každého počítače serveru.<br/>(Webové aplikace Azure) V ovládacím panelu Azure pro webovou aplikaci otevřete okno Application Insights.| [**Čítače výkonu**](../../azure-monitor/app/performance-counters.md)<br/>[**Výjimky**](asp-net-exceptions.md) - podrobné trasování zásobníku<br/>[**Závislosti**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Přidání fragmentu javascriptu na webové stránky](../../azure-monitor/app/javascript.md)|[Výkon stránky](../../azure-monitor/app/usage-overview.md), výjimky prohlížeče, výkon AJAX. Vlastní telemetrie na straně klienta.|
|[Vytvořit webové testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md)|Získejte upozornění, pokud váš web přestane být k dispozici|
|[Ujistěte se, že nástroj buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) je generován msbuildem|[Vytváření poznámk v metrických grafech](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Psaní vlastních událostí a metrik](../../azure-monitor/app/api-custom-events-metrics.md)|Spočítejte obchodní události a metriky, sledujte podrobné využití a další.|
|[Profilujte svůj živý web](https://aka.ms/AIProfilerPreview)|Podrobné časování funkcí z živé webové aplikace|






