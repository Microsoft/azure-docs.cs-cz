---
title: Analýza události prostředků infrastruktury služby Azure pomocí služby Application Insights | Microsoft Docs
description: Další informace o vizualizaci a analýzu událostí pomocí Application Insights pro monitorování a Diagnostika Azure Service Fabric clusterů.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/04/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: 3a7c7663bc13b7169ec9d31aa21365219ec39059
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Analýza události a vizualizace s Application Insights

Azure Application Insights je rozšiřitelná platforma pro monitorování aplikací a diagnostiku. Zahrnuje výkonné analýzy a zadávat dotazy na nástroj, přizpůsobitelné řídicích panelů a vizualizací a další možnosti, včetně automatizované výstrahy. Je doporučené platforma pro monitorování a Diagnostika pro Service Fabric aplikace a služby. Tento článek pomůže vyřešit následující běžné otázky

* Jak zjistím, co se děje v mé aplikací a služeb a shromáždění telemetrie
* Jak odstranit Moje aplikace, zejména služby vzájemné komunikaci
* Získání metriky o Moje služby výkonu, například čas načítání stránky, požadavky http

Účelem tohoto článku je ukazují, jak získat přehledy a řešení potíží s z v rámci aplikace statistiky. Pokud chcete další informace o nastavení a konfigurace AI s Service Fabric, podívejte se na to [kurzu](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-app-insights"></a>Monitorování v nástroji App Insights

Application Insights obsahuje s formátováním předinstalované s Service Fabric. Na stránce Přehled AI poskytuje základní informace o vaší služby, jako je například doba odezvy a počet zpracovaných požadavků. Kliknutím na tlačítko "Vyhledat" v horní části uvidíte seznam posledních požadavků v aplikaci. Kromě toho by být najdete zde chybné žádosti a diagnostikovat, jaké chyby mohlo dojít.

![Přehled AI](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

Na pravém panelu na předchozím obrázku, existují dva hlavní typy položek v seznamu: požadavky a události. Požadavky jsou volání provedená do aplikace API přes požadavky HTTP v tomto případě a události jsou vlastní události, které slouží jako telemetrie, které můžete přidat libovolné místo v kódu. Dále můžete prozkoumat instrumentace aplikací v [Application Insights API pro vlastní události a metriky](../application-insights/app-insights-api-custom-events-metrics.md). Kliknutím na žádost o by zobrazte další podrobnosti o jak je znázorněno na následujícím obrázku, včetně dat, které jsou specifické pro Service Fabric, která se shromažďují v balíčku nuget AI Service Fabric. Tyto informace jsou užitečné pro řešení potíží a zároveň budete vědět, co je stav aplikace a všechny tyto informace je vyhledávat v rámci Application Insights

![Podrobnosti požadavku AI](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights obsahuje určené zobrazení pro dotazování na všechna data, která se dodává v. Klikněte nahoře na stránce Přehled přejděte na portálu AI "Průzkumníku metrik". Pro vlastní události uvedeno nahoře, požadavků, výjimky, čítače výkonu a další metriky pomocí dotazu jazyka Kusto sem můžete spouštět dotazy. Následující příklad ukazuje všechny žádosti za poslední 1 hodinu.

![Podrobnosti požadavku AI](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Pokud chcete prozkoumat další možnosti na portálu Statistika aplikace, přejděte na [dokumentaci k portálu služby Application Insights](../application-insights/app-insights-dashboards.md).

### <a name="configuring-ai-with-wad"></a>Konfigurace s WAD AI

>[!NOTE]
>Používá se pouze pro clustery s Windows v tuto chvíli.

Existují dva primární způsoby odesílání dat z WAD AI Azure, které se dosáhne přidáním jímky AI WAD konfigurace, jak je podrobně uvedeno v [v tomto článku](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Při vytváření clusteru na portálu Azure přidejte kód instrumentace AI

![Přidání AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Při vytváření clusteru, pokud je vypnuté Diagnostika "Na", se zobrazí volitelné pole k zadání klíč instrumentace Application Insights. Pokud můžete vložit klíč AI, je podřízený AI automaticky nakonfigurován pro můžete v šabloně Resource Manager, který se používá k nasazení clusteru.

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Přidat do šablony Resource Manageru jímky AI

V "WadCfg" šablony Resource Manageru přidejte "Podřízený" zahrnutím následující dvě změny:

1. Přidat konfiguraci podřízený přímo po deklarování z `DiagnosticMonitorConfiguration` dokončení:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Zahrnout jímky v `DiagnosticMonitorConfiguration` přidáním následujícího řádku `DiagnosticMonitorConfiguration` z `WadCfg` (bezprostředně před `EtwProviders` jsou deklarovány):

    ```json
    "sinks": "applicationInsights"
    ```

V obou předchozích fragmenty kódu byl použit název "applicationInsights" k popisu jímky. Toto není požadavek a také název jímky je součástí "jímky", název můžete nastavit na libovolný řetězec.

V současné době protokolů z clusteru zobrazují jako **trasování** v prohlížeči na AI protokolu. Vzhledem k tomu, že většina trasování pocházejících z platformy je úrovně "Informační", můžete také zvážit změna konfigurace podřízený jenom k odesílání protokolů typu "Kritický" nebo "Chyba". Stačí přidáním "Kanály" podřízený, jak je předvedeno v [v tomto článku](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Pokud používáte nesprávné klíč AI portálu nebo v šabloně Resource Manager, budete muset ručně změnit klíč a aktualizovat cluster / zavést jej znovu.

### <a name="configuring-ai-with-eventflow"></a>Konfigurace s EventFlow AI

Pokud používáte EventFlow pro agregaci událostí, je třeba importovat `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`balíček NuGet. Následující kód je vyžadována ve *výstupy* části *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Zajistěte, aby proveďte požadované změny v filtry a také zahrnovat všechny ostatní vstupy (spolu s jejich odpovídajících balíčky NuGet).

## <a name="aisdk"></a>AI. SADA SDK

Doporučuje se použít EventFlow a WAD jako řešení agregace, protože umožňují pro více modulární přístup k diagnostiky a monitorování, tj. Pokud chcete změnit vaše výstupy z EventFlow, vyžaduje nijak nemění skutečné instrumentace, stačí Jednoduchá změna konfiguračního souboru. Pokud však rozhodnete investovat do pomocí Application Insights a pravděpodobně nedojde ke změně na jiné platformě, by měla vypadat do aplikace pomocí AI na novou sadu SDK pro agregaci událostí a jejich odesláním AI. To znamená, že se již muset nakonfigurovat EventFlow odeslání dat do AI, ale místo toho nainstaluje balíček Service Fabric NuGet ApplicationInsight. Informace o balíčku naleznete [zde](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Podpora služby Application Insights pro Mikroslužeb a kontejnery](https://azure.microsoft.com/en-us/blog/app-insights-microservices/) uvedeny některé nové funkce, které se pracuje (aktuálně stále v beta), které umožňují, abyste měli bohatší možnosti monitorování se na pole s AI. Jedná se o závislost sledování (používá se při vytváření AppMap služeb a aplikací v clusteru a komunikace mezi nimi) a lepší korelace trasování pocházejících z vašich služeb (pomáhá v lepší přesným rozpoznáním problém v pracovním postupu aplikace nebo služby).

Pokud jsou vývoj v rozhraní .NET a budou pravděpodobně používat některé z Service Fabric programovací modely a jsou ochotni použít AI jako svou platformu pro vizualizaci a analýzu dat událostí a protokolů, pak doporučujeme přejít prostřednictvím sady SDK AI trasy jako pracovní postup monitorování a Diagnostika. Čtení [to](../application-insights/app-insights-asp-net-more.md) a [to](../application-insights/app-insights-asp-net-trace-logs.md) začít pracovat s pomocí AI shromáždit a zobrazit protokoly.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Navigace AI prostředků na portálu Azure

Po nakonfigurování AI jako výstupu událostí a protokoly informace začněte objeví v prostředku AI za pár minut. Přejděte k prostředku AI, kterým se dostanete na řídicí panel AI prostředků. Klikněte na tlačítko **vyhledávání** na hlavním panelu AI chcete zobrazit nejnovější trasování, které obdržela a možné filtrovat pomocí nich.

*Průzkumníku metrik* je užitečným nástrojem pro vytvoření vlastní řídicí panely podle metriky, které vaše aplikace, služby a cluster může být vytváření sestav. V tématu [zkoumat metriky ve službě Application Insights](../application-insights/app-insights-metrics-explorer.md) nastavit několik grafy pro sami na základě dat shromažďujete.

Kliknutím na tlačítko **Analytics** se dostanete na portál Application Insights analýzy, kde může dotazovat událostí a trasování s větší rozsah a volitelnost. Další informace o to v [Analytics ve službě Application Insights](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>Další postup

* [Nastavení výstrah v AI](../application-insights/app-insights-alerts.md) oznámení o změnách v výkonu a využití
* [Inteligentní detekce ve službě Application Insights](../application-insights/app-insights-proactive-diagnostics.md) provede proaktivní analýzu telemetrie odesílána AI varovat před potenciálním problémům s výkonem
