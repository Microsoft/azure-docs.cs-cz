---
title: Application Insights pro webové aplikace v jazyce Java, které jsou již za provozu
description: Spustit monitorování webové aplikace, která je již spuštěna na serveru
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/10/2016
ms.author: mbullwin
ms.openlocfilehash: edefb6637dae2ff00144f0b7c07ad974430d096b
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794543"
---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>Application Insights pro webové aplikace v jazyce Java, které jsou již za provozu

Pokud máte webovou aplikaci, která je již spuštěna na serveru J2EE, můžete začít monitorovat její [Application Insights](app-insights-overview.md) bez nutnosti provádět změny kódu nebo nové kompilace projektu. Pomocí této možnosti získáte informace o požadavky HTTP odeslané na serveru, neošetřených výjimek a čítače výkonu.

Budete potřebovat předplatné [Microsoft Azure](https://azure.com).

> [!NOTE]
> Postup na této stránce přidá SDK do vaší webové aplikace za běhu. Tento modul runtime instrumentace je užitečné, pokud nechcete aktualizovat nebo znovu sestavte vašeho zdrojového kódu. Pokud je to možné, doporučujeme, ale [přidejte sadu SDK ke zdrojovému kódu](app-insights-java-get-started.md) místo. Získáte další možnosti, jako je například psaní kódu pro sledování činnosti uživatelů.
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Získejte klíč instrumentace Application Insights
1. Přihlaste se k [portálu Microsoft Azure](https://portal.azure.com)
2. Vytvořte nový prostředek Application Insights a nastavte typ aplikace na webovou aplikaci Java.
   
    ![Zadejte název, vyberte webovou aplikaci Java a klikněte na možnost Vytvořit](./media/app-insights-java-live/02-create.png)

    Prostředek se vytvoří během pár sekund.

4. Otevřete nový prostředek a získat svůj klíč instrumentace. Tento klíč budete muset za chvíli vložit do projektu kódu.
   
    ![V přehledu nového prostředku klikněte na tlačítko Vlastnosti a zkopírujte klíč instrumentace](./media/app-insights-java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2. Stažení sady SDK
1. Stáhněte si [Application Insights SDK pro jazyk Java](https://aka.ms/aijavasdk). 
2. Na serveru extrahujte obsah sady SDK k adresáři, ze které jsou načtena binární soubory vašeho projektu. Pokud používáte Tomcat, se tento adresář by obvykle v části `webapps/<your_app_name>/WEB-INF/lib`

Všimněte si, že je potřeba tento postup opakujte pro každou instanci serveru a pro každou aplikaci.

## <a name="3-add-an-application-insights-xml-file"></a>3. Přidání souboru xml Application Insights
Vytvořte soubor ApplicationInsights.xml do složky, ve kterém můžete přidat sadu SDK. Umístěte do ní následující kód XML.

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
* Korelace událostí je doplněk komponenty požadavku HTTP. Přiřadí identifikátor každé žádosti přijaté serverem a přidá ho jako vlastnost každé položce telemetrie jako vlastnost Operation.Id. Umožňuje korelovat telemetrii související s každou žádostí nastavením filtru v [diagnostické vyhledávání](app-insights-diagnostic-search.md).

## <a name="4-add-an-http-filter"></a>4. Přidat filtr HTTP
Vyhledejte a otevřete soubor web.xml ve vašem projektu a slučte následující fragment kódu pod uzlem webové aplikace, které jsou nakonfigurované filtry aplikace.

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
Možná budete muset [nastavit výjimky, které odesílat odchozí data](app-insights-ip-addresses.md).

## <a name="6-restart-your-web-app"></a>6. Restartování webové aplikace
## <a name="7-view-your-telemetry-in-application-insights"></a>7. Zobrazte telemetrii ve službě Application Insights
Vraťte se do prostředku Application Insights na web [Microsoft Azure Portal](https://portal.azure.com).

Telemetrická data o požadavcích HTTP se zobrazí v okně Přehled. (Pokud zde nejsou, počkejte několik sekund a pak klikněte na tlačítko Aktualizovat.)

![ukázková data](./media/app-insights-java-live/5-results.png)

Proklikejte se prostřednictvím jakékoli grafu pro zobrazení podrobnějších metrik. 

![](./media/app-insights-java-live/6-barchart.png)

A při zobrazení vlastností požadavku, uvidíte telemetrické události související s například požadavky a výjimkami.

![](./media/app-insights-java-live/7-instance.png)

[Další informace o metrikách.](app-insights-metrics-explorer.md)

## <a name="next-steps"></a>Další postup
* [Přidání telemetrických údajů do webových stránek](app-insights-javascript.md) monitorování zobrazení stránek a metrik uživatele.
* [Nastavit testy webu](app-insights-monitor-web-app-availability.md) a ujistěte se vaše aplikace zůstává aktivní a reagující.
* [Zaznamenat trasování protokolu](app-insights-java-trace-logs.md)
* [Hledání událostí a protokolů](app-insights-diagnostic-search.md) k diagnostice potíží.
* [Konfigurace aplikace inicializátoru pružiny spouštěcí](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights)
