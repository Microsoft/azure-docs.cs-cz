---
title: Analýza událostí azure service fabric s přehledy aplikací
description: Další informace o vizualizaci a analýze událostí pomocí Application Insights pro monitorování a diagnostiku clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 5c80dc9f350fec30469a9bce2ed836c276ef3a7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464750"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Analýza událostí a vizualizace pomocí Application Insights

Součást Azure Monitor, Application Insights je rozšiřitelná platforma pro monitorování aplikací a diagnostiku. Obsahuje výkonný analytický a dotazovací nástroj, přizpůsobitelný řídicí panel a vizualizace a další možnosti včetně automatického upozorňování. Integrace Application Insights s Service Fabric zahrnuje nástroje pro Visual Studio a portál Azure, stejně jako metriky specifické pro service fabric, které poskytují komplexní prostředí protokolování out-of-the-box. Přestože mnoho protokolů jsou automaticky vytvořeny a shromažďovány pro vás pomocí Application Insights, doporučujeme přidat další vlastní protokolování do aplikací vytvořit bohatší diagnostické prostředí.

Tento článek pomáhá řešit následující běžné otázky:

* Jak poznám, co se děje v mé aplikaci a službách a sbírám telemetrii?
* Jak lze vyřešit potíže s aplikací, zejména službami, které spolu komunikují?
* Jak získám metriky o tom, jak si moje služby vedou, například čas načítání stránky, požadavky HTTP?

Účelem tohoto článku je ukázat, jak získat přehledy a řešení potíží z aplikace Přehledy. Pokud se chcete dozvědět, jak nastavit a nakonfigurovat Application Insights pomocí Service Fabric, podívejte se na tento [kurz](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Monitorování v přehledech aplikací

Application Insights má bohaté prostředí po vybalení z krabice při používání Service Fabric. Na stránce s přehledem application insights poskytuje klíčové informace o vaší službě, jako je doba odezvy a počet zpracovaných požadavků. Kliknutím na tlačítko "Hledat" nahoře se ve vaší aplikaci zobrazí seznam posledních požadavků. Navíc byste mohli zobrazit neúspěšné požadavky zde a diagnostikovat, jaké chyby mohou nastat.

![Přehledy aplikací – přehled](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

Na pravém panelu v předchozím obrázku jsou v seznamu dva hlavní typy položek: požadavky a události. Požadavky jsou volání rozhraní API aplikace prostřednictvím požadavků HTTP v tomto případě a události jsou vlastní události, které fungují jako telemetrie, kterou můžete přidat kdekoli ve vašem kódu. Můžete dále prozkoumat instrumentaci aplikací v [application insights API pro vlastní události a metriky](../azure-monitor/app/api-custom-events-metrics.md). Kliknutím na požadavek by se zobrazí další podrobnosti, jak je znázorněno na následujícím obrázku, včetně dat specifických pro Service Fabric, který je shromážděn v balíčku nuget služby Application Insights Service Fabric. Tyto informace jsou užitečné pro řešení potíží a vědět, jaký je stav vaší aplikace, a všechny tyto informace lze prohledávat v rámci přehledů aplikací.

![Podrobnosti o požadavku na přehledy aplikací](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights má určené zobrazení pro dotazování proti všechna data, která přichází. Kliknutím na "Průzkumník metrik" v horní části stránky Přehled přejděte na portál Application Insights. Zde můžete spouštět dotazy na dříve uvedené vlastní události, požadavky, výjimky, čítače výkonu a další metriky pomocí dotazovacího jazyka Kusto. Následující příklad ukazuje všechny požadavky za poslední 1 hodinu.

![Podrobnosti o požadavku na přehledy aplikací](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Chcete-li dále prozkoumat možnosti portálu Application Insights, přejděte do [dokumentace k portálu Application Insights](../azure-monitor/app/app-insights-dashboards.md).

### <a name="configuring-application-insights-with-eventflow"></a>Konfigurace přehledů aplikací pomocí EventFlow

Pokud používáte EventFlow k agregaci `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`událostí, nezapomeňte importovat balíček NuGet. Následující kód je vyžadován v části *výstupy* *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Ujistěte se, že provést požadované změny ve filtrech, stejně jako zahrnout všechny ostatní vstupy (spolu s jejich příslušné balíčky NuGet).

## <a name="application-insights-sdk"></a>Aplikace Přehledy SDK

Doporučuje se používat EventFlow a WAD jako agregační řešení, protože umožňují modulárnější přístup k diagnostice a monitorování, tj. jednoduché úpravy konfiguračního souboru. Pokud se však rozhodnete investovat do používání Application Insights a není pravděpodobné, že se změníte na jinou platformu, měli byste se podívat na použití nové sady SDK application insights pro agregaci událostí a jejich odeslání do Application Insights. To znamená, že již nebudete muset nakonfigurovat EventFlow pro odesílání dat do Application Insights, ale místo toho nainstaluje balíček ApplicationInsight Service Fabric NuGet. Podrobnosti o balíčku naleznete [zde](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Podpora Application Insights pro mikroslužby a kontejnery](https://azure.microsoft.com/blog/app-insights-microservices/) zobrazuje některé nové funkce, na kterých se pracuje (aktuálně stále v beta verzi), které umožňují mít bohatší možnosti monitorování inasvit ázidu s Application Insights. Patří mezi ně sledování závislostí (používá se při vytváření AppMap všech vašich služeb a aplikací v clusteru a komunikace mezi nimi) a lepší korelace trasování pocházejících z vašich služeb (pomáhá lépe určit problém v pracovním postupu aplikace nebo služby).

Pokud vyvíjíte v rozhraní .NET a pravděpodobně budete používat některé programovací modely Service Fabric a jste ochotni použít Application Insights jako platformu pro vizualizaci a analýzu dat událostí a protokolu, doporučujeme přejít přes Application Insights SDK jako pracovní postup monitorování a diagnostiky. Přečtěte si [to](../azure-monitor/app/asp-net-more.md) a [to,](../azure-monitor/app/asp-net-trace-logs.md) abyste mohli začít pomocí Application Insights ke shromažďování a zobrazování protokolů.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navigace v prostředku Application Insights na webu Azure Portal

Jakmile nakonfigurujete Application Insights jako výstup pro vaše události a protokoly, informace by se měly začít zobrazovat ve vašem prostředku Application Insights během několika minut. Přejděte na prostředek Application Insights, který vás přenese na řídicí panel prostředků Application Insights. Kliknutím na **Hledat** na hlavním panelu Přehledy aplikací zobrazíte nejnovější trasování, které obdržel, a budete je moci procházet.

*Průzkumník metrik* je užitečný nástroj pro vytváření vlastních řídicích panelů na základě metrik, které vaše aplikace, služby a cluster mohou vykazovat. Podívejte [se na tématu Zkoumání metrik v Application Insights](../azure-monitor/app/metrics-explorer.md) a nastavte si několik grafů na základě dat, která shromažďujete.

Kliknutím na **Analytics** přejdete na portál Application Insights Analytics, kde můžete dotazovat události a trasování s větším rozsahem a volitelnostmi. Další informace najdete v [analytice v application insights](../azure-monitor/app/analytics.md).

## <a name="next-steps"></a>Další kroky

* [Nastavení upozornění v umělou a inici,](../azure-monitor/app/alerts.md) která mají být upozorňována na změny výkonu nebo využití
* [Inteligentní detekce v Application Insights](../azure-monitor/app/proactive-diagnostics.md) provádí proaktivní analýzu telemetrie odesílané do Application Insights, aby vás upozornila na potenciální problémy s výkonem
