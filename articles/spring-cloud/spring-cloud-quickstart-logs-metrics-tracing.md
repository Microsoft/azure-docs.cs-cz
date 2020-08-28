---
title: Rychlý Start – monitorování jarních cloudových aplikací Azure pomocí protokolů, metrik a trasování
description: Využijte streamování protokolů, Log Analytics, metriky a trasování a sledujte Piggymetrics ukázkové aplikace v Azure jarním cloudu.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: f9f03c355e1e619d004c8ec8c1cc2f91932db744
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046829"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Rychlý Start: monitorování jarních cloudových aplikací Azure díky protokolům, metrikám a trasování

Díky integrované možnosti monitorování v Azure jaře cloudu můžete ladit a monitorovat složité problémy. Jarní cloud Azure integruje [jarní Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) s využitím Azure [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Tato integrace poskytuje výkonné protokoly, metriky a možnosti distribuované vektorizace z Azure Portal. Následující postupy vysvětlují, jak používat streamování protokolů, Log Analytics, metriky a distribuované trasování s nasazenými aplikacemi PiggyMetrics.

## <a name="prerequisites"></a>Požadavky

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

1. Vyberte **Azure Explorer**a pak **jarní Cloud**.

1. Klikněte pravým tlačítkem na spuštěnou aplikaci.

1. V rozevíracím seznamu vyberte možnost **protokoly streamování** .

   ![Výběr protokolů streamování](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Vyberte **instanci**.

   ![Vybrat instanci](media/spring-cloud-intellij-howto/select-instance.png)
    
1. Protokol streamování se zobrazí v okně výstup.

   ![Výstup protokolu streamování](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. Přejít ke **službě | **Na stránce Přehled a v části **monitorování** vyberte **protokoly** . Klikněte na **Spustit** v některém z ukázkových dotazů pro jarní cloud Azure. 

   [![Položka ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) Log Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Pak se zobrazí filtrované protokoly. Další pokyny k zápisu dotazů najdete v tématu [Azure Log Analytics docs](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries) .

   [![Dotaz na ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png) analýzu protokolů](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Metriky

1. Přejít ke **službě | **Na stránce Přehled a v části **monitorování** vyberte **metriky** . Přidejte svoji první metriku výběrem `system.cpu.usage` možnosti **metrika** a `Avg` **agregace** , abyste viděli časovou osu celkového využití CPU.

   [![Položka ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png) metriky](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Klikněte na tlačítko **Přidat filtr** na panelu nástrojů výše a vyberte možnost `App=Gateway` Zobrazit využití procesoru pouze pro aplikaci **brány** .

   [![Použití filtru v metrikách ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Zavřete filtr, který jste vytvořili, klikněte na **použít rozdělení** a `App` Vyberte **hodnoty** , abyste viděli využití CPU různými aplikacemi.

   [![Použití rozdělení v metrikách ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Distribuované trasování

1. Přejít ke **službě | **Na stránce Přehled a v části **monitorování** vyberte **distribuované trasování** . Pak na pravé straně klikněte na kartu **Zobrazit mapu aplikací** .

   [![Položka ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) distribuované trasování](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Teď můžete zobrazit stav volání mezi Piggymetrics aplikacemi. 

   [![Přehled ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png) distribuovaného trasování](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Kliknutím na odkaz mezi **bránou** a **účtem – službu** zobrazíte další podrobnosti, jako jsou nejpomalejší volání metodou http.

   [![Distribuované trasování ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Nakonec klikněte na **prozkoumat výkon** a prozkoumejte výkonnější integrovanou analýzu výkonu.

   [![Výkon ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) distribuovaného trasování](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu prostředků z portálu nebo spuštěním následujícího příkazu v Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

V předchozích krocích jste také nastavili výchozí název skupiny prostředků. Pokud chcete tuto výchozí hodnotu vymazat, spusťte v Cloud Shell následující příkaz:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Další kroky

Další informace o možnostech sledování, které se nachází v rámci služby Azure Pramenitého cloudu, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Diagnostické služby](diagnostic-services.md) 
>  [Distribuované trasování](spring-cloud-tutorial-distributed-tracing.md) 
>  [Streamování protokolů v reálném čase](spring-cloud-howto-log-streaming.md)
