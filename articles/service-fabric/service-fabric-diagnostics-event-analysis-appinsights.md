---
title: Analýza událostí služby Azure Service Fabric pomocí Application Insights | Dokumentace Microsoftu
description: Další informace o vizualizaci a analýzu událostí pomocí služby Application Insights pro monitorování a diagnostiku clustery Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: f4c620bbb0e17abfacb504866230786a971ff409
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393154"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Události analýzy a vizualizace s využitím Application Insights

Součást služby Azure Monitor, Application Insights je rozšiřitelná platforma pro monitorování a diagnostiku aplikací. Obsahuje výkonné analytické a dotazování na nástroj, přizpůsobitelného řídicího panelu a vizualizací a další možnosti, včetně automatické upozorňování. Integrace Application Insights s využitím Service Fabric obsahuje nástroje prostředí pro Visual Studio a Azure portal, stejně jako konkrétní metriky Service Fabric, zajistit prostředí s komplexní out-of-the-box protokolování. Přestože mnoho protokoly se automaticky vytváří a shromážděné za vás pomocí Application Insights, doporučujeme přidat další vlastní protokolování do vašich aplikací pro vytvoření pohodlnější a pestřejší prostředí diagnostiky.

Tento článek pomůže řešit běžné otázky, na následující:

* Jak zjistím, co se děje v mé aplikace a služby a shromažďování telemetrických dat?
* Jak řešit Moje aplikace, zejména služby komunikaci mezi sebou?
* Jak získat metriky o jak Moje služby fungují, například, doba načítání stránek, požadavky HTTP

Účelem tohoto článku je ukazují, jak zkoumat a řešit z v rámci Application Insights. Pokud chcete získat informace tom, jak vytvořit a nakonfigurovat Application Insights s využitím Service Fabric, najdete v tomto [kurzu](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Monitorování ve službě Application Insights

Application Insights má bohaté mimo prostředí pole pomocí Service Fabric. Na stránce Přehled služby Application Insights poskytuje klíčové informace o službě, jako je například doba odezvy a počet požadavků zpracovaných. Kliknutím na tlačítko "Vyhledat" v horní části uvidíte seznam nedávné žádosti o ve vaší aplikaci. Kromě toho bude moct zobrazit zde neúspěšné žádosti a diagnostikovat, jakým chybám došlo.

![Přehled služby Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

V pravém panelu na předchozím obrázku, existují dva hlavní typy položek v seznamu: požadavky a události. Požadavky jsou volání provedená do aplikace API pomocí požadavku HTTP v tomto případě a události jsou vlastní události, které fungují jako telemetrická data, která můžete přidat libovolné místo v kódu. Můžete podrobněji prozkoumat instrumentace vaší aplikace v [API pro Application Insights pro vlastní události a metriky](../azure-monitor/app/api-custom-events-metrics.md). Kliknutím na žádost o zobrazí další podrobnosti o jak je znázorněno na následujícím obrázku, včetně dat specifických pro Service Fabric, která se shromažďují v balíčku nuget Application Insights Service Fabric. Tyto informace je užitečná pro řešení potíží a porozumění tomu, co je stav aplikace a všechny tyto informace je prohledávatelná v rámci Application Insights

![Podrobnosti o žádosti Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights má určené zobrazení pro dotazování na data, která se dodává v. Klikněte na tlačítko "Průzkumníka metrik" nahoře na stránce Přehled přejít na portál Application Insights. Tady můžete spouštět dotazy proti vlastní události, které už bylo uvedeno dříve, požadavky, výjimky, čítače výkonu a další metriky pomocí dotazovacího jazyka Kusto. Následující příklad ukazuje všechny požadavky, za poslední 1 hodinu.

![Podrobnosti o žádosti Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Prozkoumat další možnosti portálu služby Application Insights, přejděte na [portálu dokumentace k Application Insights](../azure-monitor/app/app-insights-dashboards.md).

### <a name="configuring-application-insights-with-eventflow"></a>Konfigurace Application Insights s využitím EventFlow

Pokud používáte využitím EventFlow pro agregaci událostí, ujistěte se, že k importu `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`balíček NuGet. Následující kód je nutné v *výstupy* část *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Ujistěte se, že proveďte požadované změny v filtry, jakož i zahrnout všechny ostatní vstupy (spolu s jejich příslušné balíčky NuGet).

## <a name="application-insights-sdk"></a>Application Insights SDK

Doporučuje se použít využitím EventFlow a WAD jako agregace řešení, protože umožňují modulárnější přístup na diagnostiku a sledování, například pokud chcete změnit vaše výstupy z využitím EventFlow, nevyžaduje žádné změny vaší skutečné instrumentace, pouze jednoduchých úprav do konfiguračního souboru. Pokud však rozhodnout investovat do pomocí Application Insights a pravděpodobně nemohou změnit na různé platformy, by měl vypadat do pomocí nové sady SDK služby Application Insights pro agregaci událostí a odesílá je do služby Application Insights. To znamená, že se už nemusíte konfigurovat využitím EventFlow to poslat data do Application Insights, ale místo toho nainstaluje balíček Service Fabric NuGet ApplicationInsight. Najdete podrobnosti o balíček [tady](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Podpora služby Application Insights pro Mikroslužby a kontejnery](https://azure.microsoft.com/blog/app-insights-microservices/) uvádí některé nové funkce, které se pracuje (stále aktuálně v beta verze), což vám umožní mít bohatší možnosti monitorování out-of-the-box pomocí Application Insights. Patří mezi ně závislost sledování (používá se při vytváření AppMap všech služeb a aplikací v clusteru a komunikace mezi nimi) a lepší korelace trasování z vašich služeb (pomáhá v lepší přesné určení problém v pracovním postupu aplikace nebo služby).

Pokud vyvíjíte v rozhraní .NET a budou pravděpodobně používat některé z programovacích modelů Service Fabric a jsou natolik, jak pomocí Application Insights jako platformu pro vizualizaci a analýzu dat událostí a protokolů, pak doporučujeme přejít přes Application Insights Sady SDK route jako pracovní postup monitorování a diagnostiku. Čtení [to](../azure-monitor/app/asp-net-more.md) a [to](../azure-monitor/app/asp-net-trace-logs.md) začít s ke shromáždění a zobrazení protokolů pomocí Application Insights.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Přejděte prostředku Application Insights na webu Azure portal

Po nakonfigurování služby Application Insights jako výstup pro události a protokoly byste začít informace zobrazí v prostředku Application Insights za pár minut. Přejděte do prostředku Application Insights, který se dostanete na řídicí panel prostředku Application Insights. Klikněte na tlačítko **hledání** na hlavním panelu služby Application Insights a zobrazte nejnovější trasování, které se přijaly a bude moct filtrovat projde.

*Průzkumník metrik* je užitečným nástrojem pro vytváření vlastních řídicích panelů na základě metrik, které mohou být vytváření sestav aplikací, služeb a clusteru. Zobrazit [zkoumání metriky v Application Insights](../azure-monitor/app/metrics-explorer.md) nastavit několik grafů pro sami na základě dat shromažďujete.

Kliknutím na **Analytics** se dostanete na portál Application Insights Analytics, kde můžete zadávat dotazy událostmi a sledováním s větší rozsah a volitelnost. Další informace najdete v [Analytics ve službě Application Insights](../azure-monitor/app/analytics.md).

## <a name="next-steps"></a>Další postup

* [Nastavte si upozornění v umělé Inteligenci](../azure-monitor/app/alerts.md) abyste dostávali oznámení o změny ve výkonu a využití
* [Inteligentní zjišťování ve službě Application Insights](../azure-monitor/app/proactive-diagnostics.md) provádí proaktivní analýzu telemetrických dat odesílaných do Application Insights upozornit vás na potenciální problémy s výkonem
