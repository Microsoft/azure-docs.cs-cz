---
title: Podpora Azure Application Insights pro více komponent, mikroslužby a kontejnery | Dokumentace Microsoftu
description: Monitorování aplikací, které se skládají z několika komponent nebo role pro výkon a využití.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: mbullwin
ms.openlocfilehash: bf247748415822d5ba1a0e652fdeff384d8e8db1
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982047"
---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Monitorování aplikace s více komponentami pomocí Application Insights (preview)

Můžete monitorovat aplikace, které se skládají z několika součástí serveru, role nebo služby s [Azure Application Insights](app-insights-overview.md). Stav součásti a vztahy mezi nimi jsou zobrazeny v rámci jedné mapy aplikace. Můžou sledovat jednotlivé operace, ať už několik součástí automatické korelace HTTP. Diagnostiky kontejnerů můžete integrovat a korelují s telemetrie application. Použijte jeden prostředek Application Insights pro všechny součásti vaší aplikace. 

![Mapa aplikace s více komponentami](./media/app-insights-monitor-multi-role-apps/application-map-001.png)

K označení jakékoli funkční součást rozsáhlé aplikace tady používáme "součást". Například typické obchodní aplikace se může skládat z kódu klienta, které jsou spuštěné v prohlížečích s jeden nebo další webové aplikace služby, které pak použijte zpět ukončení služby. Součásti serveru může být hostovaná místně na v cloudu, nebo může být Azure webové a pracovní role nebo může spustit v kontejnerech, jako je Docker nebo Service Fabric. 

### <a name="sharing-a-single-application-insights-resource"></a>Sdílení na jeden prostředek Application Insights 

Klíčovou techniku má odesílat telemetrická data z každé komponenty v aplikaci pro stejný prostředek Application Insights, ale použít `cloud_RoleName` vlastnost k rozlišení komponent, pokud je to nezbytné. Přidá sada SDK služby Application Insights `cloud_RoleName` generovat vlastnost součástí telemetrická data. Například sada SDK přidá název webového serveru nebo název služby role `cloud_RoleName` vlastnost. Tuto hodnotu telemetryinitializer můžete přepsat. Mapa aplikace používá `cloud_RoleName` vlastnost k identifikaci komponenty na mapě.

Přepsání pro další informace o tom, `cloud_RoleName` viz vlastnost [přidat vlastnosti: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer).  

V některých případech to nemusí být vhodné a možná dáte přednost použití samostatné prostředky pro různé skupiny součástí. Například budete muset použít různé prostředky pro účely fakturace nebo správy.

S výstrahou budeme předpokládat ve zbývající části tohoto dokumentu, který chcete odeslat data z několika součástí na jeden prostředek Application Insights.

## <a name="configure-multi-component-applications"></a>Konfigurace aplikace s více komponentami

Chcete-li získat více komponent aplikace mapy, potřebujete k dosažení těchto cílů:

* **Nainstalujte nejnovější předběžnou verzi** balíček Application Insights v jednotlivých komponentách aplikace. 
* **Sdílet jeden prostředek Application Insights** pro všechny součásti vaší aplikace.
* **Povolit mapa kompozitní aplikace** v okně verze Preview.

Konfiguraci jednotlivých komponent vaší aplikace pomocí vhodnou metodu pro jeho typu. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), [JavaScript](app-insights-javascript.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. Nainstalujte nejnovější předběžnou verzi balíčku

Aktualizovat nebo nainstalovat balíčky Application Insights v projektu pro každou komponentu serveru. Pokud používáte Visual Studio:

1. Klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**. 
2. Vyberte **zahrnout předběžné verze**.
3. Pokud se balíčky Application Insights se zobrazí v aktualizace, vyberte je. 

    V opačném případě Procházet a instalovat příslušný balíček:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - pro komponenty běžící jako spustitelných souborů hosta a kontejnerů Dockeru v aplikaci Service Fabric
    * Microsoft.ApplicationInsights.ServiceFabric.Native - pro služby reliable services v aplikacích ServiceFabric
    * Microsoft.ApplicationInsights.Kubernetes pro komponenty běžící v Dockeru v Kubernetes

### <a name="2-share-a-single-application-insights-resource"></a>2. Sdílet jeden prostředek Application Insights

* V sadě Visual Studio, klikněte pravým tlačítkem na projekt a vyberte **konfigurovat Application Insights**, nebo **Application Insights > Konfigurovat**. Pro první projekt použijte průvodce k vytvoření prostředku Application Insights. Pro následující projekty vyberte stejný prostředek.
* Pokud není žádná nabídka Application Insights, nakonfigurujte ručně:

   1. V [webu Azure portal](https://portal,azure.com), otevřete prostředek Application Insights již vytvořena pro jinou komponentou.
   2. V okně přehledu, otevřít rozevírací nabídce základy kartu a zkopírujte **Instrumentační klíč.**
   3. Ve vašem projektu otevřete soubor ApplicationInsights.config a vložit: `<InstrumentationKey>your copied key</InstrumentationKey>`

![Zkopírujte Instrumentační klíč do souboru .config](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-composite-application-map"></a>3. Povolit mapa kompozitní aplikace

Na webu Azure Portal otevřete prostředek vaší aplikace. V části Konfigurace dílčí záhlaví, klikněte na verze Preview a otevřete tak okno verze Preview. V okně verze Preview povolit *mapa kompozitní aplikace*.

### <a name="4-enable-docker-metrics-optional"></a>4. Zapnutí metrik Docker (volitelné) 

Pokud se komponenta spustí v Dockeru hostované na Virtuálním počítači Windows Azure, můžete shromažďovat další metriky z kontejneru. Vložte tento ve vaší [diagnostiky Azure](../monitoring-and-diagnostics/azure-diagnostics.md) konfiguračního souboru:

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

## <a name="use-cloudrolename-to-separate-components"></a>Použít cloud_RoleName oddělit součásti

`cloud_RoleName` Vlastnost je připojen k veškeré telemetrii. Identifikuje komponenty - roli nebo službě - pocházející telemetrická data. (Není stejný jako cloud_RoleInstance, která odděluje stejné role, které jsou spuštěny paralelně na několika počítačích nebo serverové procesy.)

Na portálu můžete filtrovat nebo segment telemetrických dat pomocí této vlastnosti. V tomto příkladu se v okně chyb vyfiltruje a zobrazí pouze informace z front-endové webové službě, odfiltrováním selhání z back-endového rozhraní API CRM:

![Grafu metriky segmentované podle název cloudové Role](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>Trasování operací mezi součástmi

Můžete sledovat z jedné komponenty do druhého volání při zpracování jednotlivých operací.


![Zobrazit telemetrická data pro operaci](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Proklikejte se k seznamu korelační telemetrická data pro tuto operaci v klientské části webového serveru a back endového rozhraní API:

![Hledat v součásti](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Další postup

* [Samostatné telemetrie z vývoj, testování a produkce](app-insights-separate-resources.md)
