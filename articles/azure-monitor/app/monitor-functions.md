---
title: Monitorování aplikací běžících na Azure Functions s Application Insights-Azure Monitor | Microsoft Docs
description: Azure Monitor hladce se integruje s vaší aplikací běžící na Azure Functions a umožňuje vám monitorovat výkon a obcházet problémy s aplikacemi v žádné době.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/20/2020
ms.openlocfilehash: a936c77abb9aed5886fae8b2ec4a10bb076b7cb5
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83776738"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Azure Functions monitorování pomocí Azure Monitor Application Insights

[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) nabízí integrovanou integraci s Azure Application Insights k monitorování funkcí. 

Application Insights shromažďuje údaje o protokolech, výkonu a chybách a automaticky detekuje anomálie výkonu. Application Insights obsahuje výkonné analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, jak se vaše funkce používají. Když máte přehled o datech aplikace, můžete průběžně vylepšit výkon a použitelnost. Můžete dokonce použít Application Insights během vývoje projektu místní funkce aplikace Function App. 

Požadovaná Application Insights instrumentace je integrovaná do Azure Functions. Jediná věc, kterou potřebujete, je platný klíč instrumentace pro připojení aplikace Function App k prostředku Application Insights. Klíč instrumentace by se měl přidat do nastavení vaší aplikace, když se v Azure vytvoří prostředek Function App. Pokud vaše aplikace Function ještě tento klíč nemá, můžete ji nastavit ručně. Další informace najdete v tématu [monitorování Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring?tabs=cmd).

## <a name="distributed-tracing-for-java-applications-public-preview"></a>Distribuované trasování pro aplikace Java (Public Preview)


> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Public Preview pro Java Azure Functions. Pro plán spotřeby má nespustilý počátek v chladu 8-9 sekund.

Pokud jsou vaše aplikace napsané v jazyce Java, můžete zobrazit rozsáhlejší data z aplikací Functions, včetně požadavků, závislostí, protokolů a metrik. Další data také umožňují zobrazit a diagnostikovat koncové transakce a zobrazit mapu aplikace, která agreguje mnoho transakcí, aby zobrazovala topologické zobrazení toho, jak systémy pracují a jaké jsou průměrné výkonové a chybové míry.

Komplexní Diagnostika a mapa aplikace poskytují přehled o jedné transakci/požadavku. Tyto dvě funkce jsou společně užitečné při hledání hlavní příčiny problémů se spolehlivostí a kritických bodů výkonu na základě jednotlivých požadavků.

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Jak povolit distribuované trasování pro aplikace funkcí Java?

Přejděte na okno Přehled aplikace Functions, přejděte do části konfigurace. V části nastavení aplikace klikněte na + nastavení nové aplikace. Přidejte následující dvě nastavení aplikace s nižšími hodnotami a potom v levém horním rohu klikněte na Uložit. Hotovo!

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si další pokyny a informace o monitorování [monitorování Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
* Získání přehledu o [distribuovaném trasování](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)
* Podívejte se, co [Mapa aplikace](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) může pro vaši firmu dělat
* Přečtěte si o [požadavcích a závislostech pro aplikace v jazyce Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) .
* Další informace o [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) a [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)