---
title: Application Insights pro webové aplikace v Javě, které jsou již live
description: Zahájení monitorování webové aplikace, která je již spuštěna na serveru
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/10/2016
ms.author: mbullwin
ms.openlocfilehash: ab101802aa881d4f4c6377299164c4408c673e7c
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53980686"
---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>Application Insights pro webové aplikace v Javě, které jsou již live

Pokud máte webovou aplikaci, která je již běží na vašem serveru J2EE, můžete začít monitorovat pomocí [Application Insights](../../application-insights/app-insights-overview.md) bez nutnosti měnit kód nebo znovu zkompilovat váš projekt. Pomocí této možnosti můžete získat informace o požadavky HTTP odeslané na server, neošetřené výjimky a čítače výkonu.

Budete potřebovat předplatné [Microsoft Azure](https://azure.com).

> [!NOTE]
> Postup na této stránce přidá SDK do vaší webové aplikace za běhu. Tento modul runtime instrumentace je užitečná, pokud nechcete aktualizovat nebo znovu sestavte svůj zdrojový kód. Pokud je to možné, doporučujeme vám ale [přidejte sadu SDK ke zdrojovému kódu](java-get-started.md) místo. Který poskytuje další možnosti, jako je například zápis kódu pro sledování činnosti uživatelů.
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Získejte klíč instrumentace Application Insights
1. Přihlaste se k [portálu Microsoft Azure](https://portal.azure.com)
2. Vytvořte nový prostředek Application Insights a nastavte typ aplikace do webové aplikace v Javě.
   
    ![Zadejte název, vyberte webovou aplikaci Java a klikněte na možnost Vytvořit](./media/java-live/02-create.png)

    Prostředek se vytvoří během několika sekund.

4. Otevřete nový prostředek a získejte svůj Instrumentační klíč. Tento klíč budete muset za chvíli vložit do projektu kódu.
   
    ![V přehledu nového prostředku klikněte na tlačítko Vlastnosti a zkopírujte klíč instrumentace](./media/java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2. Stáhnout sadu SDK
1. Stáhněte si [Application Insights SDK pro jazyk Java](https://aka.ms/aijavasdk). 
2. Na serveru extrahujte obsah sady SDK do adresáře, ze kterého se načtou vaše binární soubory projektu. Pokud používáte Tomcat, tento adresář by obvykle v části `webapps/<your_app_name>/WEB-INF/lib`

Všimněte si, že budete muset tento postup opakujte pro každou instanci serveru a pro každou aplikaci.

## <a name="3-add-an-application-insights-xml-file"></a>3. Přidání souboru xml Application Insights
Vytvořte soubor ApplicationInsights.xml do složky, ve které jste přidali do sady SDK. Vložte do něj následující kód XML.

Nahraďte klíč instrumentace, který jste dostali z portálu Azure.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```

* Klíč instrumentace se zasílá společně s každou položkou telemetrie a říká službě Application Insights, aby ho zobrazila v prostředku.
* Požadavek komponenty HTTP je volitelný. Automaticky odesílá telemetrii týkající se žádostí a časů odezvy na portál.
* Korelace událostí je doplněk komponenty požadavku HTTP. Přiřadí identifikátor každé žádosti přijaté serverem a přidá ho jako vlastnost každé položce telemetrie jako vlastnost Operation.Id. Umožňuje korelovat telemetrii související s každou žádostí nastavením filtru [diagnostické vyhledávání](../../azure-monitor/app/diagnostic-search.md).

## <a name="4-add-an-http-filter"></a>4. Přidat filtr HTTP
Najděte a otevřete soubor web.xml ve vašem projektu a sloučit následující fragment kódu pod uzlem webové aplikace, které jsou nakonfigurované filtry aplikace.

Chcete-li získat nejpřesnější výsledky, musí být filtr namapován před všemi ostatními filtry.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>
```

## <a name="5-check-firewall-exceptions"></a>5. Kontrola výjimky brány firewall
Možná budete muset [nastavit výjimky, které odesílání odchozích dat](../../azure-monitor/app/ip-addresses.md).

## <a name="6-restart-your-web-app"></a>6. Restartujte webovou aplikaci
## <a name="7-view-your-telemetry-in-application-insights"></a>7. Zobrazte telemetrii ve službě Application Insights
Vraťte se do prostředku Application Insights na web [Microsoft Azure Portal](https://portal.azure.com).

Telemetrická data týkající se požadavků HTTP se zobrazí na kartě s přehledem. (Pokud zde nejsou, počkejte několik sekund a pak klikněte na tlačítko Aktualizovat.)

![ukázková data](./media/java-live/5-results.png)

Proklikejte se prostřednictvím jakékoli grafu pro zobrazení podrobnějších metrik. 

![](./media/java-live/6-barchart.png)

A při zobrazení vlastností požadavku uvidíte telemetrické události související s například požadavky a výjimky.

![](./media/java-live/7-instance.png)

[Další informace o metrikách.](../../application-insights/app-insights-metrics-explorer.md)

## <a name="next-steps"></a>Další postup
* [Přidání telemetrie na webové stránky](javascript.md) pro monitorování zobrazení stránek a metrik uživatele.
* [Nastavte testy webů](../../azure-monitor/app/monitor-web-app-availability.md) abyste měli jistotu, vaše aplikace zůstává aktivní a reagující.
* [Záznam trasování protokolu](java-trace-logs.md)
* [Prohledávejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) na pomoc s diagnostikou problémů.
* [Nakonfigurovat aplikaci Spring Boot inicializátor](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights)
