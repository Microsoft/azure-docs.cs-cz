---
title: Jak používat Micrometer s Azure Application Insights Java SDK
description: Krok za krokem průvodce na používání Micrometer s aplikací Insights jarní boot a non-jarní spouštěcí aplikace.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 11/01/2018
ms.openlocfilehash: dd04087db32f0bbfa75dafa7e12c355e5ab7b515
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670062"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Jak používat Micrometer s Azure Application Insights Java SDK
Monitorování aplikací mikrometru měří metriky pro kód aplikace založené na JVM a umožňuje exportovat data do vašich oblíbených monitorovacích systémů. Tento článek vás naučí, jak používat Micrometer s Application Insights pro jarní i nejarní spouštěcí aplikace.

## <a name="using-spring-boot-15x"></a>Použití jarního startu 1,5x
Přidejte do souboru pom.xml nebo build.gradle následující závislosti: 
* [Application Insights startér](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter) 2.5.0 nebo novější
* Mikrometr Azure Registry 1.1.0 nebo vyšší
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 nebo vyšší (to to backports autoconfig kód v rámci jaro).
* [Prostředek ApplicationInsights](../../azure-monitor/app/create-new-resource.md )

Kroky

1. Aktualizujte soubor pom.xml aplikace Spring Boot a přidejte do ní následující závislosti:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. Aktualizujte soubor application.properties nebo yml pomocí klíče Application Insights Instrumentation pomocí následující vlastnosti:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Sestavte si aplikaci a spusťte
2. Výše uvedené by vám mělo zprovoznit předem agregované metriky, které se automaticky shromažďují na Azure Monitoru. Podrobnosti o tom, jak doladit startér jarního startu Application Insights, najdete v [readme na GitHubu](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Použití pružiny 2.x

Přidejte do souboru pom.xml nebo build.gradle následující závislosti:

* Application Insights Startér pro jarní startovací 2.1.2 nebo vyšší
* Startéry Azure-spring-boot-metrics 2.0.7 nebo novější
* [Prostředek Přehledy aplikací](../../azure-monitor/app/create-new-resource.md )

Kroky:

1. Aktualizujte soubor pom.xml aplikace Spring Boot a přidejte do ní následující závislost:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Aktualizujte soubor application.properties nebo yml pomocí klíče Application Insights Instrumentation pomocí následující vlastnosti:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Sestavte si aplikaci a spusťte
4. Výše uvedené by vám mělo zavést běh s předem agregovanými metrikami, které se automaticky shromažďují na Azure Monitoru. Podrobnosti o tom, jak doladit startér jarního startu Application Insights, najdete v [readme na GitHubu](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Výchozí metriky:

*    Automaticky nakonfigurované metriky pro Tomcat, JVM, Logback Metriky, Log4J metriky, Metriky provozu, Metriky procesoru, FileDescriptorMetrics.
*    Například, pokud Netflix Hystrix je přítomen na cestě třídy dostaneme tyto metriky stejně. 
*    Následující metriky mohou být k dispozici přidáním příslušných fazolí. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcastCache, JCache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - Metriky OkHttp3 
        - Kafka Metriky 

 

Jak vypnout automatické metriky kolekce: 
 
- Metriky JVM: 
    - management.metrics.binders.jvm.enabled=false 
- Metriky přihlašování: 
    - management.metrics.binders.logback.enabled=false
- Metriky doby uptime: 
    - management.metrics.binders.uptime.enabled=false 
- Metriky procesoru:
    -  management.metrics.binders.processor.enabled=false 
- FileDescriptorMetrics:
    - management.metrics.binders.files.enabled=false 
- Hystrix Metrics, pokud knihovna na cestě pro třídy: 
    - management.metrics.binders.hystrix.enabled=false 
- AspectJ Metriky, pokud knihovna na cestě pro třídy: 
    - spring.aop.enabled=false 

> [!NOTE]
> Zadejte výše uvedené vlastnosti v souboru application.properties nebo application.yml aplikace Spring Boot

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Použití mikrometru s webovými aplikacemi, které nejsou součástí jarního spouštění

Přidejte do souboru pom.xml nebo build.gradle následující závislosti:

* Application Insights Web Auto 2.5.0 nebo novější
* Mikrometr Azure Registry 1.1.0 nebo vyšší
* [Prostředek Přehledy aplikací](../../azure-monitor/app/create-new-resource.md )

Kroky:

1. Do souboru pom.xml nebo build.gradle přidejte následující závislosti:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0</version>
        </dependency>
     ```

2. Vložte `ApplicationInsights.xml` soubor do složky prostředků:

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
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
       </TelemetryInitializers>
    
    </ApplicationInsights>
    ```

3. Ukázková třída Servlet (vyzařuje metriku časovače):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

4. Ukázková třída konfigurace:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

Další informace o metrikách naleznete v [dokumentaci k mikrometru](https://micrometer.io/docs/).

Další ukázkový kód o tom, jak vytvořit různé typy metrik, najdete v[oficiálním úložišti Micrometer GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Jak vytvořit vazbu další metriky kolekce

### <a name="springbootspring"></a>SpringBoot/Pružina

Vytvořte si zobák příslušné kategorie metriky. Řekněme například, že potřebujeme metriky mezipaměti Guava:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Existuje několik metrik, které nejsou ve výchozím nastavení povoleny, ale mohou být vázány výše uvedeným způsobem. Kompletní seznam najdete [v oficiálním úložišti Micrometer GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Aplikace, které nejsou pružinové
Do konfiguračního souboru přidejte následující kód vazby:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Další kroky

* Další informace o mikrometru naleznete v oficiální [dokumentaci k mikrometru](https://micrometer.io/docs).
* Další informace o jarním zasedání azure najdete v oficiální [dokumentaci spring on Azure](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable).
