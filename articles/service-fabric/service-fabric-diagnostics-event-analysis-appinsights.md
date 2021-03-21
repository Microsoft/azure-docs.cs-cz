---
title: Analýza událostí v Azure Service Fabric s využitím Application Insights
description: Seznamte se s vizualizací a analýzou událostí pomocí Application Insights pro monitorování a diagnostiku clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: f4d77cc3b2f2182b02d47f047070b819daa5f3c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589013"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Analýza a vizualizace událostí pomocí Application Insights

Součást Azure Monitor, Application Insights je rozšiřitelná platforma pro monitorování a diagnostiku aplikací. Zahrnuje výkonné nástroje pro analýzu a dotazování, přizpůsobitelné řídicí panely a vizualizace a další možnosti, včetně automatizovaného upozorňování. Application Insights integrace s Service Fabric zahrnuje nástroje pro sadu Visual Studio a Azure Portal a také Service Fabric specifické metriky, které poskytují ucelené možnosti protokolování. I když se pro vás Application Insights bude automaticky vytvářet a shromažďovat spousty protokolů, doporučujeme, abyste do svých aplikací přidali další vlastní protokolování, abyste mohli vytvořit bohatší prostředí diagnostiky.

Tento článek pomáhá řešit následující běžné otázky:

* Návody o tom, co se v mých aplikacích a službách prochází a jak shromažďovat telemetrii?
* Návody řešit problémy s aplikací, zejména o službách, které vzájemně komunikují?
* Návody získat metriky, jak fungují moje služby, například čas načítání stránky, požadavky HTTP?

Účelem tohoto článku je Ukázat, jak získat přehledy a řešit potíže v rámci Application Insights. Pokud se chcete dozvědět, jak nastavit a nakonfigurovat Application Insights pomocí Service Fabric, přečtěte si tento [kurz](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Monitorování v Application Insights

Application Insights při použití Service Fabric nabízí bohatě funkční prostředí. Na stránce Přehled Application Insights poskytuje klíčové informace o vaší službě, například dobu odezvy a počet zpracovaných požadavků. Kliknutím na tlačítko Hledat v horní části uvidíte seznam posledních požadavků v aplikaci. Kromě toho by bylo možné zobrazit neúspěšné požadavky a diagnostikovat, k jakým chybám mohlo dojít.

![Přehled Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

Na pravém panelu na předchozím obrázku existují dva hlavní typy záznamů v seznamu: žádosti a události. Požadavky jsou volání rozhraní API aplikace prostřednictvím požadavků HTTP v tomto případě a události jsou vlastní události, které fungují jako telemetrie, které můžete přidat kdekoli v kódu. Můžete dále prozkoumat instrumentaci aplikací v [rozhraní Application Insights API pro vlastní události a metriky](../azure-monitor/app/api-custom-events-metrics.md). Kliknutím na žádost se zobrazí další podrobnosti, jak je znázorněno na následujícím obrázku, včetně dat specifických pro Service Fabric, která se shromažďují v balíčku NuGet Application Insights Service Fabric. Tyto informace jsou užitečné při řešení potíží a znalosti toho, co je stav aplikace, a všechny tyto informace jsou prohledávatelné v rámci Application Insights

![Snímek obrazovky, který obsahuje další podrobnosti, včetně dat specifických pro Service Fabric, které se shromažďují v balíčku NuGet Application Insights Service Fabric.](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights má určené zobrazení pro dotazování na všechna data, která jsou součástí. V horní části stránky přehled vyberte "Průzkumník metrik", chcete-li přejít na portál Application Insights. Tady můžete spouštět dotazy proti vlastním událostem, které jsou uvedené před, požadavky, výjimky, čítače výkonu a další metriky pomocí dotazovacího jazyka Kusto. Následující příklad zobrazuje všechny žádosti za poslední 1 hodinu.

![Podrobnosti o Application Insights žádosti](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Chcete-li dále prozkoumat možnosti portálu Application Insights, přečtěte si dokumentaci k [portálu Application Insights Portal](../azure-monitor/app/overview-dashboard.md).

### <a name="configuring-application-insights-with-eventflow"></a>Konfigurace Application Insights pomocí využitím eventflow

Pokud k agregaci událostí používáte využitím eventflow, nezapomeňte importovat `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` balíček NuGet. Následující kód je vyžadován v části *výstupy* *eventFlowConfig.jsv*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Ujistěte se, že ve svých filtrech provedete požadované změny, a taky Zahrňte všechny další vstupy (spolu s příslušnými balíčky NuGet).

## <a name="application-insights-sdk"></a>Sada Application Insights SDK

Doporučuje se používat využitím eventflow a WAD jako řešení agregace, protože umožňují obecnější přístup k diagnostice a monitorování. to znamená, že pokud chcete změnit své výstupy z využitím eventflow, nemusíte mít žádnou změnu na skutečnou instrumentaci, a to pouze jednoduchou úpravou konfiguračního souboru. Pokud se rozhodnete investovat do používání Application Insights a nebudete se pravděpodobně měnit na jinou platformu, měli byste se podívat na použití Application Insights nové sady SDK pro agregaci událostí a jejich posílání do Application Insights. To znamená, že už nebudete muset konfigurovat využitím eventflow, aby se vaše data odesílala Application Insights, ale místo toho se nainstaluje balíček NuGet pro Service Fabric ApplicationInsight. Podrobnosti o balíčku najdete [tady](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Application Insights podpora mikroslužeb a kontejnerů](https://azure.microsoft.com/blog/app-insights-microservices/) zobrazuje některé nové funkce, které jsou právě zpracovávány (aktuálně stále ve verzi beta), což vám umožní mít širší možnosti monitorování s Application Insights. Mezi ně patří sledování závislostí (používá se při vytváření AppMap všech služeb a aplikací v clusteru a komunikace mezi nimi) a lepší korelace trasování přicházejících z vašich služeb (pomáhá lépe určit problém v pracovním postupu aplikace nebo služby).

Pokud vyvíjíte v rozhraní .NET a pravděpodobně použijete některé z programovacích modelů Service Fabric a máte ochotni používat Application Insights jako platformu pro vizualizaci a analýzu dat událostí a protokolů, pak doporučujeme, abyste provedli jako pracovní postup monitorování a diagnostiky trasu Application Insights SDK. Přečtěte si dokumentaci [Application Insights dokumentace](../azure-monitor/azure-monitor-app-hub.yml) a [protokoly trasování](../azure-monitor/app/asp-net-trace-logs.md) , které vám pomohou začít používat Application Insights k shromažďování a zobrazování protokolů.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navigace prostředku Application Insights v Azure Portal

Jakmile nakonfigurujete Application Insights jako výstup pro události a protokoly, měly by se informace během několika minut zobrazovat v prostředku Application Insights. Přejděte na prostředek Application Insights, který vás převezme na řídicí panel prostředků Application Insights. Výběrem možnosti **Hledat** na hlavním panelu Application Insights zobrazíte nejnovější trasování, která přijala, a budete je moct přes ně filtrovat.

*Průzkumník metrik* je užitečný nástroj pro vytváření vlastních řídicích panelů na základě metrik, které mohou hlásit aplikace, služby a cluster. V tématu [prozkoumávání metrik v Application Insights](../azure-monitor/essentials/metrics-charts.md) můžete nastavit několik grafů pro sebe na základě shromažďovaných dat.

Kliknutím na **Analýza** přejdete na portál Application Insights Analytics, kde můžete zadávat dotazy na události a trasování s větším rozsahem a volitelnosti. Přečtěte si další informace o [analýze v Application Insights](../azure-monitor/logs/log-query-overview.md).

## <a name="next-steps"></a>Další kroky

* [V AI nastavte výstrahy](../azure-monitor/alerts/alerts-log.md) na změny výkonu nebo využití.
* [Inteligentní zjišťování v Application Insights](../azure-monitor/app/proactive-diagnostics.md) provádí proaktivní analýzu telemetrie, která se posílá do Application Insights upozorňující na potenciální problémy s výkonem.
