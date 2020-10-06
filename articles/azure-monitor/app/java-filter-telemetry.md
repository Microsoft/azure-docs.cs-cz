---
title: Filtrování telemetrie Azure Application Insights ve webové aplikaci Java
description: Zmenšení provozu telemetrie filtrováním událostí, které nepotřebujete monitorovat.
ms.topic: conceptual
ms.date: 3/14/2019
ms.custom: devx-track-java
ms.openlocfilehash: e450cf0de7dce4f626fd41252bfeed5fba294c70
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761001"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrování telemetrie ve webové aplikaci Java

Filtry poskytují způsob, jak vybrat telemetrii, kterou vaše [Webová aplikace v jazyce Java odesílá Application Insights](java-get-started.md). K dispozici jsou některé předem připravené filtry, které můžete použít, a můžete také napsat vlastní filtry.

Filtry dodané po box zahrnují:

* Úroveň závažnosti trasování
* Konkrétní adresy URL, klíčová slova nebo kódy odpovědí
* Rychlé odpovědi – to znamená, že žádosti, na které vaše aplikace reaguje, rychle
* Konkrétní názvy událostí

> [!NOTE]
> Filtry zkosí metriky vaší aplikace. Můžete se třeba rozhodnout, že při diagnostice pomalých odpovědí nastavíte filtr, který zahodí dobu rychlé odezvy. Je ale nutné si uvědomit, že průměrné doby odezvy hlášené nástrojem Application Insights budou pomalejší než skutečná rychlost a počet požadavků bude menší než skutečný počet.
> Pokud se to týká, použijte místo toho [vzorkování](./sampling.md) .

## <a name="setting-filters"></a>Nastavení filtrů

V ApplicationInsights.xml přidejte `TelemetryProcessors` oddíl podobný tomuto příkladu:


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

[Zkontrolujte kompletní sadu integrovaných procesorů](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal).

## <a name="built-in-filters"></a>Předdefinované filtry

### <a name="metric-telemetry-filter"></a>Filtr telemetrie metriky

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` -Čárkami oddělený seznam názvů vlastních metrik.


### <a name="page-view-telemetry-filter"></a>Filtr telemetrie zobrazení stránky

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` -Doba trvání odkazuje na čas potřebný k načtení stránky. Pokud je tato nastavení nastavena, nejsou hlášeny stránky, které jsou načteny rychleji než tento čas.
* `NotNeededNames` -Čárkami oddělený seznam názvů stránek.
* `NotNeededUrls` -Čárkami oddělený seznam fragmentů adresy URL. `"home"`Filtruje například všechny stránky, které mají v adrese URL "Home".


### <a name="request-telemetry-filter"></a>Vyžádat filtr telemetrie


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Filtr syntetického zdroje

Odfiltruje všechny telemetrie, které mají hodnoty vlastnosti SyntheticSource. Patří mezi ně požadavky z testů roboty, Spider a dostupnosti.

Odfiltrovat telemetrii pro všechny syntetické požadavky:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Odfiltrovat telemetrii pro konkrétní syntetické zdroje:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` -Čárkami oddělený seznam syntetických názvů zdrojů.

### <a name="telemetry-event-filter"></a>Filtr událostí telemetrie

Filtruje vlastní události (protokolované pomocí [TrackEvent ()](./api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` -Čárkami oddělený seznam názvů událostí.


### <a name="trace-telemetry-filter"></a>Trasovat filtr telemetrie

Filtruje trasování protokolů (protokolované pomocí [TrackTrace ()](./api-custom-events-metrics.md#tracktrace) nebo [kolektoru rozhraní protokolování](java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` platné hodnoty jsou:
  *  Odfiltrovat všechna trasování
  *  TRACE – bez filtrování. je rovno úrovni trasování
  *  INFO – filtrování úrovně trasování
  *  UPOZORNIT a vyfiltrovat trasování a informace
  *  Chyba: upozornění na vyfiltrování, informace, trasování
  *  KRITICKÉ – vyfiltrujte všechny, ale kritické.


## <a name="custom-filters"></a>Vlastní filtry

### <a name="1-code-your-filter"></a>1. kódování filtru

V kódu vytvořte třídu, která implementuje `TelemetryProcessor` :

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


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. vyvolejte filtr v konfiguračním souboru.

V ApplicationInsights.xml:

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

### <a name="3-invoke-your-filter-java-spring"></a>3. vyvolání filtru (pružina Java)

Pro aplikace založené na architektuře pružiny musí být vlastní procesory telemetrie registrovány v hlavní třídě aplikace jako Bob. Po spuštění aplikace se pak budou autodrátovat.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

Budete muset vytvořit vlastní parametry filtru v nástroji `application.properties` a využít k předání těchto parametrů do vlastního filtru nahlasovou architekturu jarních konfigurací. 


## <a name="troubleshooting"></a>Poradce při potížích

*Můj filtr nefunguje.*

* Ověřte, zda jste zadali platné hodnoty parametrů. Například doba trvání by měla být celá čísla. Neplatné hodnoty způsobí, že se filtr ignoruje. Pokud vlastní filtr vyvolá výjimku z konstruktoru nebo metody set, bude ignorována.

## <a name="next-steps"></a>Další kroky

* [Vzorkování](./sampling.md) – zvažte vzorkování jako alternativu, která nezkosí vaše metriky.

