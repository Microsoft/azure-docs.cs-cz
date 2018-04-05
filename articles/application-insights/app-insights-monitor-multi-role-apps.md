---
title: Podpora Azure Application Insights pro několik komponent, mikroslužeb a kontejnery | Microsoft Docs
description: Monitorování aplikací, které se skládají z několika součástí nebo role pro výkonu a využití.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/17/2017
ms.author: mbullwin
ms.openlocfilehash: 9b03aff140eec5b355383447f0a815220d6408e3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Monitorování více součásti aplikací s nástrojem Application Insights (preview)

Můžete monitorovat aplikace, které se skládají z více součásti serveru, role nebo služby s [Azure Application Insights](app-insights-overview.md). Zobrazí se stav součásti a vztahy mezi nimi na jednu mapu aplikace. Mohou sledovat jednotlivých operací prostřednictvím více součástí, které se automatické korelace HTTP. Kontejner diagnostiky můžete integrovat a korelační s telemetrií aplikace. Použijte jeden prostředek Application Insights pro všechny součásti aplikace. 

![Mapa více součásti aplikace](./media/app-insights-monitor-multi-role-apps/app-map.png)

Rozumí jakékoli funkční součástí velké aplikace tady používáme "součást". Například typické obchodní aplikace se může skládat z klienta kód spuštěný ve webové prohlížeče, rozhovoru na jeden nebo více webové aplikace služby, které pak použijte zpět ukončení služby. Součásti serveru může být hostovaná místně na v cloudu, nebo může být Azure webové a pracovní role nebo může spustit v kontejnerech například Docker nebo Service Fabric. 

### <a name="sharing-a-single-application-insights-resource"></a>Sdílení jednoho prostředku Application Insights 

Zde klíče technika je odesílat telemetrická data z každé komponenty v aplikaci do stejného zdroje Application Insights, ale použít `cloud_RoleName` vlastnost k rozlišení součásti, pokud je to nezbytné. Přidá Application Insights SDK `cloud_RoleName` emitování vlastnost součástí telemetrie. Například sada SDK přidá název webového serveru nebo název role Služba pro `cloud_RoleName` vlastnost. Tato hodnota se telemetryinitializer můžete přepsat. Mapování aplikací používá `cloud_RoleName` vlastnost k identifikaci součásti na mapě.

Další informace o přepsání `cloud_RoleName` vlastnost najdete [přidávat vlastnosti: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer).  

V některých případech to nemusí být vhodné a chcete používat samostatný prostředky pro různé skupiny součástí. Například budete muset použít jiné prostředky pro správu nebo pro účely fakturace. Pomocí samostatných prostředků znamená, že nevidíte všechny součásti, na které se zobrazí na jednu mapu aplikace; a že nemůže zadat dotaz mezi komponentami v [Analytics](app-insights-analytics.md). Také budete muset nastavit samostatnou prostředky.

S výstrahou budeme předpokládat ve zbývající části tohoto dokumentu, který chcete odesílat data z několika součástí jeden prostředek Application Insights.

## <a name="configure-multi-component-applications"></a>Konfigurace více součásti aplikací

Chcete-li získat více součásti aplikace mapy, těchto cílů dosáhnout:

* **Nainstalujte nejnovější předběžnou verzi** balíček Application Insights v jednotlivých součástí aplikace. 
* **Sdílet jeden prostředek Application Insights** pro všechny součásti aplikace.
* **Povolit kompozitních aplikací mapy** v okně verze Preview.

Nakonfigurujte jednotlivé komponenty pomocí příslušné metody pro tento typ aplikace. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), [JavaScript](app-insights-javascript.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. Instalovat nejnovější balíček předběžné verze

Aktualizujte nebo instalaci balíčků Application Insights v projektu pro každou součást serveru. Pokud používáte Visual Studio:

1. Klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**. 
2. Vyberte **zahrnout předběžné verze**.
3. Pokud je balíčky zobrazí v aktualizace, vybrat Application Insights. 

    Jinak vyhledávat a instalovat příslušný balíček:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - pro součásti spuštěný jako Host spustitelných souborů a Docker kontejnery spuštění aplikace v Service Fabric
    * Microsoft.ApplicationInsights.ServiceFabric.Native - pro spolehlivé služby v aplikacích ServiceFabric
    * Microsoft.ApplicationInsights.Kubernetes pro součásti systémem Kubernetes v Docker

### <a name="2-share-a-single-application-insights-resource"></a>2. Sdílet jeden prostředek Application Insights

* V sadě Visual Studio, klikněte pravým tlačítkem na projekt a vyberte **konfigurovat Application Insights**, nebo **Application Insights > Konfigurovat**. Pro první projekt pomocí průvodce vytvořte prostředek Application Insights. Pro další projekty vyberte stejný prostředek.
* Pokud není žádná nabídka Application Insights, nakonfigurujte ručně:

   1. V [portál Azure](https://portal,azure.com), otevřete prostředek Application Insights jste již vytvořili pro jiné komponenty.
   2. V okně přehledu, otevřete rozevírací Essentials kartě a zkopírujte **klíč instrumentace.**
   3. V projektu otevřete soubor ApplicationInsights.config a vložit: `<InstrumentationKey>your copied key</InstrumentationKey>`

![Zkopírovat klíč instrumentace do souboru .config](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-composite-application-map"></a>3. Povolit mapy kompozitních aplikací

Na portálu Azure otevřete prostředek vaší aplikace. V části Konfigurace dílčí záhlaví, klikněte na verze Preview a otevřete okno verze Preview. V okně verze Preview povolit *kompozitních aplikací mapy*.

### <a name="4-enable-docker-metrics-optional"></a>4. Povolit Docker metriky (volitelné) 

Pokud součást běží v Docker, hostované na virtuálním počítači Windows Azure, můžete shromažďovat další metriky z kontejneru. Vložte tuto ve vaší [Azure diagnostics](../monitoring-and-diagnostics/azure-diagnostics.md) konfigurační soubor:

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## <a name="use-cloudrolename-to-separate-components"></a>Použít cloud_RoleName jednotlivé komponenty

`cloud_RoleName` Vlastnost je připojen k všechny telemetrie. Identifikuje komponentu - k roli nebo službě -, který pochází telemetrii. (Není stejný jako cloud_RoleInstance, který odděluje identické rolí, které běží souběžně na více procesy serveru nebo počítače.)

Na portálu můžete filtrovat nebo segmentovat telemetrie používání této vlastnosti. V tomto příkladu je v okně selhání vyfiltrovány a zobrazí se jenom informace ze služby front-endové webové filtrování chyb z back-end CRM rozhraní API:

![Metriky grafu segmentované podle názvu Role cloudu](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>Operace trasování mezi součástmi

Můžete sledovat z jedné součásti na jiný, volání provedená při zpracování jednotlivých operací.


![Zobrazit telemetrii pro operaci](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Klikněte na tlačítko prostřednictvím korelační seznam telemetrická data pro tuto operaci v klientské části webového serveru a rozhraní API back-end:

![Hledání mezi komponentami](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Další postup

* [Samostatné telemetrie z vývoj, testování a provozním](app-insights-separate-resources.md)
