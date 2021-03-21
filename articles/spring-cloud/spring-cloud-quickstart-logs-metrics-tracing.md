---
title: Rychlý Start – monitorování jarních cloudových aplikací Azure pomocí protokolů, metrik a trasování
description: Využijte streamování protokolů, Log Analytics, metriky a trasování a sledujte Piggymetrics ukázkové aplikace v Azure jarním cloudu.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: a935b34c4fcebe9128d8af0316658072e20ddfbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596204"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Rychlý Start: monitorování jarních cloudových aplikací Azure díky protokolům, metrikám a trasování

::: zone pivot="programming-language-csharp"
Díky integrované možnosti monitorování v Azure jaře cloudu můžete ladit a monitorovat složité problémy. Jarní cloud Azure integruje Steeltoe [distribuované trasování](https://steeltoe.io/docs/3/tracing/distributed-tracing) s [Application Insights](../azure-monitor/app/app-insights-overview.md)Azure. Tato integrace poskytuje výkonné protokoly, metriky a možnosti distribuované vektorizace z Azure Portal.

Následující postupy vysvětlují, jak používat streamování protokolů, Log Analytics, metriky a distribuované trasování s ukázkovou aplikací, kterou jste nasadili v předchozích rychlých startech.

## <a name="prerequisites"></a>Předpoklady

* Dokončete předchozí rychlé starty v této sérii:

  * [Zřídit Azure jaře cloudovou službu](spring-cloud-quickstart-provision-service-instance.md).
  * [Nastavte server pro konfiguraci jarního cloudu Azure](spring-cloud-quickstart-setup-config-server.md).
  * [Sestavování a nasazování aplikací](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Protokoly

Existují dva způsoby, jak zobrazit protokoly v Azure jarním cloudu: zaznamená **streamování** protokolů v reálném čase na instanci aplikace nebo **Log Analytics** pro agregované protokoly s pokročilou funkcí dotazů.

### <a name="log-streaming"></a>Streamování protokolů

Streamování protokolů můžete v Azure CLI použít pomocí následujícího příkazu.

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

Zobrazí se výstup podobný následujícímu příkladu:

```output
=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing action method Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather) - Validation state: Valid
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController[0]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Retrieved weather data from 4 planets
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing ObjectResult, writing value of type 'System.Collections.Generic.KeyValuePair`2[[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e],[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]][]'.
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
```

> [!TIP]
> Slouží `az spring-cloud app logs -h` k prozkoumávání dalších parametrů a funkcí streamu protokolů.

### <a name="log-analytics"></a>Log Analytics

1. V Azure Portal přejdete do **služby |** Na stránce Přehled a v části **monitorování** vyberte **protokoly** . Vyberte **Spustit** na jednom z ukázkových dotazů pro jarní cloud Azure.

   [![Položka ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) Log Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Upravte dotaz tak, aby se odebraly klauzule WHERE, které omezují zobrazení na upozornění a protokoly chyb.

1. Pak vyberte `Run` a zobrazíte protokoly. Další pokyny k zápisu dotazů najdete v tématu [Azure Log Analytics docs](../azure-monitor/logs/get-started-queries.md) .

   [![Dotaz na analýzu protokolů – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

## <a name="metrics"></a>Metriky

1. V Azure Portal přejdete do **služby |** Na stránce Přehled a v části **monitorování** vyberte **metriky** . První metriku přidejte tak, že v rozevíracím seznamu **metrika** vyberete jednu z metrik **.NET nebo v** rozevíracím seznamu **žádost (.NET)** , a `Avg` pro **agregaci** Zobrazte časovou osu této metriky.

   [![Vstup metrik – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)
    
1. Na panelu nástrojů klikněte na **Přidat filtr** . výběrem této možnost `App=solar-system-weather` zobrazíte informace o využití procesoru jenom pro aplikaci se **slunečním počasí** .

   [![Použití filtru v metrikách – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. Zrušte filtr vytvořený v předchozím kroku, vyberte **použít rozdělení** a vyberte `App` **hodnoty** pro zobrazení využití CPU různými aplikacemi.

   [![Použití rozdělení v metrikách – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>Distribuované trasování

1. V Azure Portal přejdete do **služby |** Na stránce Přehled a v části **monitorování** vyberte **distribuované trasování** . Pak na pravé straně vyberte kartu **Zobrazit mapu aplikací** .

   [![Položka distribuované trasování – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Nyní můžete zobrazit stav volání mezi aplikacemi. 

   [![Přehled distribuovaného trasování – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)
    
1. Pokud chcete zobrazit více podrobností, jako jsou nejpomalejší volání metodou HTTP, vyberte propojení mezi **slunečním** a **globálním-počasí – poskytovatele** .

   [![Distribuované trasování – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)
    
1. Nakonec vyberte možnost **prozkoumat výkon** a prozkoumejte výkonnější integrovanou analýzu výkonu.

   [![Výkon distribuovaného trasování – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Díky integrované možnosti monitorování v Azure jaře cloudu můžete ladit a monitorovat složité problémy. Jarní cloud Azure integruje [jarní Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) s využitím Azure [Application Insights](../azure-monitor/app/app-insights-overview.md). Tato integrace poskytuje výkonné protokoly, metriky a možnosti distribuované vektorizace z Azure Portal. Následující postupy vysvětlují, jak používat streamování protokolů, Log Analytics, metriky a distribuované trasování s nasazenými aplikacemi PiggyMetrics.

## <a name="prerequisites"></a>Předpoklady

Dokončete předchozí kroky: 

* [Zřízení instance Azure jarního cloudu](spring-cloud-quickstart-provision-service-instance.md)
* [Nastavení konfiguračního serveru](spring-cloud-quickstart-setup-config-server.md)
* [Sestavování a nasazování aplikací](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Protokoly

Existují dva způsoby, jak zobrazit protokoly v Azure jarním cloudu: zaznamená **streamování** protokolů v reálném čase na instanci aplikace nebo **Log Analytics** pro agregované protokoly s pokročilou funkcí dotazů.

### <a name="log-streaming"></a>Streamování protokolů

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/Azure-CLI)

Streamování protokolů můžete v Azure CLI použít pomocí následujícího příkazu.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

Zobrazí se protokoly takto:

[![Protokolování streamování z Azure CLI ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> Slouží `az spring-cloud app logs -h` k prozkoumávání dalších parametrů a funkcí streamování protokolů.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Postup získání protokolů pomocí Azure Toolkit for IntelliJ:

1. Vyberte **Azure Explorer** a pak **jarní Cloud**.

1. Klikněte pravým tlačítkem na spuštěnou aplikaci.

1. V rozevíracím seznamu vyberte možnost **protokoly streamování** .

   ![Výběr protokolů streamování](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Vyberte **instanci**.

   ![Vybrat instanci](media/spring-cloud-intellij-howto/select-instance.png)
    
1. Protokol streamování se zobrazí v okně výstup.

   ![Výstup protokolu streamování](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. Přejít ke **službě |** Na stránce Přehled a v části **monitorování** vyberte **protokoly** . Klikněte na **Spustit** v některém z ukázkových dotazů pro jarní cloud Azure. 

   [![Položka ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) Log Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Pak se zobrazí filtrované protokoly. Další pokyny k zápisu dotazů najdete v tématu [Azure Log Analytics docs](../azure-monitor/logs/get-started-queries.md) .

   [![Dotaz na ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png) analýzu protokolů](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Metriky

1. Přejít ke **službě |** Na stránce Přehled a v části **monitorování** vyberte **metriky** . Přidejte svoji první metriku výběrem `system.cpu.usage` možnosti **metrika** a `Avg` **agregace** , abyste viděli časovou osu celkového využití CPU.

   [![Položka ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png) metriky](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Klikněte na tlačítko **Přidat filtr** na panelu nástrojů výše a vyberte možnost `App=Gateway` Zobrazit využití procesoru pouze pro aplikaci **brány** .

   [![Použití filtru v metrikách ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Zavřete filtr, který jste vytvořili, klikněte na **použít rozdělení** a `App` Vyberte **hodnoty** , abyste viděli využití CPU různými aplikacemi.

   [![Použití rozdělení v metrikách ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Distribuované trasování

1. Přejít ke **službě |** Na stránce Přehled a v části **monitorování** vyberte **distribuované trasování** . Pak na pravé straně klikněte na kartu **Zobrazit mapu aplikací** .

   [![Položka ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) distribuované trasování](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Teď můžete zobrazit stav volání mezi Piggymetrics aplikacemi. 

   [![Přehled ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png) distribuovaného trasování](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Kliknutím na odkaz mezi **bránou** a **účtem – službu** zobrazíte další podrobnosti, jako jsou nejpomalejší volání metodou http.

   [![Distribuované trasování ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Nakonec klikněte na **prozkoumat výkon** a prozkoumejte výkonnější integrovanou analýzu výkonu.

   [![Výkon ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) distribuovaného trasování](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

V těchto rychlých startech jste vytvořili prostředky Azure, které budou nadále účtovat poplatky, pokud zůstanou ve vašem předplatném. Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu prostředků pomocí portálu nebo spuštěním následujícího příkazu v Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

V dřívějším rychlém startu jste také nastavili výchozí název skupiny prostředků. Pokud nechcete pokračovat dalším rychlým startem, vymažte tuto výchozí hodnotu spuštěním následujícího příkazu rozhraní příkazového řádku:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Další kroky

Pokud chcete prozkoumat další možnosti monitorování Azure jaře cloudu, přečtěte si:

> [!div class="nextstepaction"]
> [Diagnostické služby](diagnostic-services.md)
>
> [Distribuované trasování](spring-cloud-tutorial-distributed-tracing.md)
>
> [Streamování protokolů v reálném čase](spring-cloud-howto-log-streaming.md)
