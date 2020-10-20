---
title: Analýza telemetrie Azure Functions v Application Insights
description: Naučte se zobrazovat a dotazovat se na data telemetrie Azure Functions shromažďovaná službou a uloženou ve službě Azure Application Insights.
ms.topic: how-to
ms.date: 10/14/2020
ms.custom: contperfq2
ms.openlocfilehash: 9091d59cbc3a4cf841e28505531c89c83e705c46
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217299"
---
# <a name="analyze-azure-functions-telemetry-in-application-insights"></a>Analýza telemetrie Azure Functions v Application Insights 

Azure Functions se integruje s Application Insights, která vám umožní monitorovat aplikace Function App. Application Insights shromažďuje data telemetrie generovaná aplikací Function App, včetně informací, které vaše aplikace zapisuje do protokolů. Při vytváření aplikace Function App je obvykle povolená integrace Application Insights. Pokud vaše aplikace Function App nemá nastaven klíč instrumentace, musíte nejdřív [Povolit integraci Application Insights](configure-monitoring.md#enable-application-insights-integration). 

Ve výchozím nastavení se data shromážděná z vaší aplikace Function App ukládají do Application Insights. V [Azure Portal](https://portal.azure.com)Application Insights poskytuje rozsáhlou sadu vizualizací dat telemetrie. Můžete přejít k protokolům chyb a událostem a metrikám dotazů. Tento článek poskytuje základní příklady, jak zobrazit shromážděná data a dotazování na ně. Další informace o zkoumání dat aplikace Function App v Application Insights najdete v tématu [co je Application Insights?](../azure-monitor/app/app-insights-overview.md). 

Další informace o uchovávání dat a potenciálních nákladech na úložiště najdete [v tématu shromažďování, uchovávání a ukládání dat v Application Insights](../azure-monitor/app/data-retention-privacy.md).   

## <a name="viewing-telemetry-in-monitor-tab"></a>Zobrazení telemetrie na kartě monitorování

S [povolenou integrací Application Insights](configure-monitoring.md#enable-application-insights-integration)můžete zobrazit data telemetrie na kartě **monitorování** .

1. Na stránce Function App vyberte po nakonfigurování Application Insights funkci, která byla alespoň jednou spuštěna. Pak v levém podokně vyberte **monitor** . Vyberte možnost **aktualizovat** pravidelně, dokud se nezobrazí seznam vyvolání funkce.

   ![Seznam volání](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Může trvat až pět minut, než se seznam objeví, zatímco klient telemetrie data pro přenos na server vystaví. Zpoždění se nevztahuje na [Live Metrics Stream](../azure-monitor/app/live-stream.md). Tato služba se při načtení stránky připojí k hostiteli Functions, takže protokoly se streamují přímo na stránku.

1. Chcete-li zobrazit protokoly pro konkrétní vyvolání funkce, vyberte pro toto vyvolání odkaz na sloupec **Datum (UTC)** . Výstup protokolování pro toto vyvolání se zobrazí na nové stránce.

   ![Podrobnosti vyvolání](media/functions-monitoring/invocation-details-ai.png)

1. Zvolením možnosti **Spustit v Application Insights** zobrazíte zdroj dotazu, který načte data protokolu Azure monitor v protokolu Azure. Pokud Azure Log Analytics ve vašem předplatném používáte poprvé, budete vyzváni k jeho povolení.

1. Po povolení Log Analytics se zobrazí následující dotaz. Můžete vidět, že výsledky dotazu jsou omezené na posledních 30 dní ( `where timestamp > ago(30d)` ) a výsledky zobrazí maximálně 20 řádků ( `take 20` ). Naopak seznam podrobností o vyvolání pro vaši funkci je za posledních 30 dnů bez omezení.

   ![Seznam volání analýzy Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Další informace najdete v části [dotazování na data telemetrie](#query-telemetry-data) dále v tomto článku.

## <a name="view-telemetry-in-application-insights"></a>Zobrazit telemetrii v Application Insights

Otevření Application Insights z aplikace Function App v [Azure Portal](https://portal.azure.com):

1. Na portálu přejděte do aplikace Function App.

1. V části **Nastavení** na levé straně vyberte **Application Insights** . 

1. Pokud používáte Application Insights s vaším předplatným poprvé, budete vyzváni k jeho povolení. Pokud to chcete provést, vyberte **zapnout Application Insights**a pak na další stránce vyberte **použít** .

![Otevření Application Insights na stránce s přehledem aplikace Function App](media/functions-monitoring/ai-link.png)

Informace o tom, jak používat Application Insights, najdete v [dokumentaci k Application Insights](/azure/application-insights/). V této části jsou uvedeny některé příklady, jak zobrazit data v Application Insights. Pokud už jste obeznámeni s Application Insights, můžete přejít přímo k [oddílům, jak nakonfigurovat a přizpůsobit data telemetrie](configure-monitoring.md#configure-log-levels).

![Karta Přehled Application Insights](media/functions-monitoring/metrics-explorer.png)

Následující oblasti Application Insights mohou být užitečné při vyhodnocování chování, výkonu a chyb ve vašich funkcích:

| Prověřování | Popis |
| ---- | ----------- |
| **[Selhání](../azure-monitor/app/asp-net-exceptions.md)** |  Vytvářejte grafy a výstrahy na základě selhání funkcí a výjimek serveru. **Název operace** je název funkce. Pokud neimplementujete vlastní telemetrie pro závislosti, neobjeví se chyby v závislostech. |
| **[Výkon](../azure-monitor/app/performance-counters.md)** | Analyzujte problémy s výkonem zobrazením využití prostředků a propustnosti na **instance rolí cloudu**. Tato data o výkonu můžou být užitečná pro scénáře ladění, kde funkce bogging své základní prostředky. |
| **[Metriky](../azure-monitor/platform/metrics-charts.md)** | Vytváření grafů a výstrah založených na metrikách. Metriky zahrnují počet vyvolání funkcí, dobu provádění a míru úspěšnosti. |
| **[Živé metriky    ](../azure-monitor/app/live-stream.md)** | Zobrazit data metriky, jak jsou vytvořena téměř v reálném čase. |

## <a name="query-telemetry-data"></a>Dotazování dat telemetrie

[Application Insights Analytics](../azure-monitor/log-query/log-query-overview.md) vám umožní přístup ke všem datům telemetrie ve formě tabulek v databázi. Analýza poskytuje dotazovací jazyk pro extrakci, manipulaci a vizualizaci dat. 

Vyberte **protokoly** pro prozkoumání protokolovaných událostí nebo dotazování na ně.

![Příklad analýzy](media/functions-monitoring/analytics-traces.png)

Tady je příklad dotazu, který zobrazuje distribuci požadavků za pracovní proces za posledních 30 minut.

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

Tabulky, které jsou k dispozici, jsou zobrazeny na kartě **schéma** na levé straně. Data generovaná pomocí volání funkcí najdete v následujících tabulkách:

| Tabulka | Popis |
| ----- | ----------- |
| **trasování** | Protokoly vytvořené modulem runtime a trasování z kódu funkce. |
| **požadavky** | Jedna žádost pro každé vyvolání funkce. |
| **výjimek** | Jakékoli výjimky vyvolané modulem runtime. |
| **customMetrics** | Počet úspěšných a neúspěšných vyvolání, míra úspěšnosti a trvání. |
| **customEvents** | Události sledované modulem runtime, například: požadavky HTTP, které aktivují funkci. |
| **Čítače výkonu** | Informace o výkonu serverů, na kterých jsou spuštěny funkce. |

Ostatní tabulky jsou k dispozici pro testy dostupnosti a telemetrie klientů a prohlížečů. Můžete implementovat vlastní telemetrii a přidat do nich data.

V každé tabulce jsou některá data specifická pro konkrétní funkce v `customDimensions` poli.  Například následující dotaz načte všechna trasování, která mají úroveň protokolu `Error` .

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

Modul runtime poskytuje `customDimensions.LogLevel` pole a `customDimensions.Category` . V protokolech můžete zadat další pole, která zapíšete do kódu funkce. Příklad v jazyce C# naleznete v tématu [strukturované protokolování](functions-dotnet-class-library.md#structured-logging) v příručce pro vývojáře knihovny tříd .NET.

## <a name="consumption-plan-specific-metrics"></a>Metriky specifické pro plán spotřeby

Při spuštění v [plánu spotřeby](functions-scale.md#consumption-plan)se *náklady* na spuštění jediného spuštění funkce měří v *GB-s*. Náklady na spuštění jsou vypočítány kombinací využití paměti s dobou spuštění. Další informace najdete v tématu [odhad nákladů na plán spotřeby](functions-consumption-costs.md).

Následující dotazy telemetrie jsou specifické pro metriky, které mají vliv na náklady na spouštění funkcí v plánu spotřeby.

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Další kroky

Další informace o monitorování Azure Functions:

+ [Monitorování Azure Functions](functions-monitoring.md)
+ [Postup konfigurace monitorování pro Azure Functions](configure-monitoring.md)

