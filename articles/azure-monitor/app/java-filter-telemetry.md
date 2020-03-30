---
title: Filtrování telemetrie Azure Application Insights ve webové aplikaci Java
description: Snižte provoz telemetrie filtrováním událostí, které nepotřebujete monitorovat.
ms.topic: conceptual
ms.date: 3/14/2019
ms.openlocfilehash: 020e54132e0ca0a9f9ccf0236f94515877015637
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659913"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrování telemetrie ve webové aplikaci Java

Filtry umožňují vybrat telemetrii, kterou vaše [webová aplikace Java odesílá do Application Insights](java-get-started.md). Existují některé out-of-the-box filtry, které můžete použít, a můžete také napsat vlastní filtry.

Out-of-the-box filtry zahrnují:

* Úroveň závažnosti trasování
* Konkrétní adresy URL, klíčová slova nebo kódy odpovědí
* Rychlé odpovědi – to znamená požadavky, na které vaše aplikace rychle reagovala
* Konkrétní názvy událostí

> [!NOTE]
> Filtry zkreslují metriky vaší aplikace. Můžete se například rozhodnout, že za účelem diagnostiky pomalých odpovědí nastavíte filtr tak, aby se rychle zahodily doby odezvy. Ale musíte si být vědomi toho, že průměrná doba odezvy hlášené Application Insights pak bude pomalejší než skutečná rychlost a počet požadavků bude menší než skutečný počet.
> Pokud se jedná o problém, použijte [vzorkování](../../azure-monitor/app/sampling.md) místo.

## <a name="setting-filters"></a>Nastavení filtrů

V souboru ApplicationInsights.xml přidejte oddíl, jako je `TelemetryProcessors` tento příklad:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```




[Zkontrolujte úplnou sadu vestavěných procesorů](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>Vestavěné filtry

### <a name="metric-telemetry-filter"></a>Metrický filtr telemetrie

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded`- Seznam vlastních názvů metrik oddělených čárkami.


### <a name="page-view-telemetry-filter"></a>Telemetrie zobrazení stránky, filtr

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS`- Doba trvání se vztahuje k době načtení stránky. Pokud je nastavena, stránky, které se načetly rychleji než tentokrát, nejsou hlášeny.
* `NotNeededNames`- Seznam názvů stránek oddělených čárkami.
* `NotNeededUrls`- Seznam fragmentů adres URL oddělených čárkami. Můžete například odfiltruje všechny stránky, `"home"` které mají v adrese URL "domov".


### <a name="request-telemetry-filter"></a>Filtr telemetrie požadavku


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Syntetický zdroj, filtr

Filtruje všechny telemetrie, které mají hodnoty v SyntheticSource vlastnost. Patří mezi ně požadavky od robotů, pavouků a testy dostupnosti.

Odfiltrovat telemetrii pro všechny syntetické požadavky:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Odfiltrovat telemetrii pro specifické syntetické zdroje:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded`- Seznam syntetických zdrojových jmen oddělených čárkami.

### <a name="telemetry-event-filter"></a>Filtr události telemetrie

Filtruje vlastní události (protokolované pomocí [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames`- Seznam názvů událostí oddělených čárkami.


### <a name="trace-telemetry-filter"></a>Trasovací telemetrie, filtr

Filtry trasování protokolu (protokolované pomocí [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) nebo [protokolování kolekce architektury](java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel`platné hodnoty jsou:
  *  OFF - Odfiltrovat všechny stopy
  *  TRASOvání - Žádné filtrování. rovná se úrovni trace
  *  INFO - Odfiltrovat úroveň TRACE
  *  VAROVAT - odfiltrovat trace a INFO
  *  CHYBA - Odfiltrovat VAROVAT, INFO, TRACE
  *  CRITICAL - odfiltrovat všechny, ale kritické


## <a name="custom-filters"></a>Vlastní filtry

### <a name="1-code-your-filter"></a>1. Kódujte filtr

V kódu vytvořte třídu, `TelemetryProcessor`která implementuje :

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. Vyvolat filtr v konfiguračním souboru

V applicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

### <a name="3-invoke-your-filter-java-spring"></a>3. Vyvolat filtr (Java spring)

Pro aplikace založené na rámci spring musí být vlastní telemetrické procesory registrovány ve vaší hlavní třídě aplikace jako bean. Poté budou automaticky zapojeny při spuštění aplikace.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

Budete muset vytvořit vlastní parametry `application.properties` filtru a využít externalizované konfigurační rozhraní spring boot u dávat průchod těmto parametrům do vlastního filtru. 


## <a name="troubleshooting"></a>Řešení potíží

*Můj filtr nefunguje.*

* Zkontrolujte, zda jste zadali platné hodnoty parametrů. Například doby trvání by měla být celá čísla. Neplatné hodnoty způsobí, že filtr bude ignorován. Pokud vlastní filtr vyvolá výjimku z konstruktoru nebo metody set, bude ignorována.

## <a name="next-steps"></a>Další kroky

* [Vzorkování](../../azure-monitor/app/sampling.md) – zvažte vzorkování jako alternativu, která nezkosení metriky.
