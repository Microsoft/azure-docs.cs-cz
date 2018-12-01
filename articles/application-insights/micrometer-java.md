---
title: Jak používat Micrometer pomocí Azure Application Insights Java SDK | Dokumentace Microsoftu
description: 'Podrobný průvodce na Micrometer pomocí aplikace Application Insights Spring Boot a Spring Boot. '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: lagayhar
ms.openlocfilehash: d579a9b628ac515877c1977f700940547e96f0c0
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52726606"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Jak používat Micrometer pomocí Azure Application Insights Java SDK
Micrometer monitorování míry metriky aplikací pro aplikace založené na JVM kódu a umožňuje exportovat data do své oblíbené systémy pro monitorování. V tomto článku se dozvíte, jak používat Micrometer pro Spring Boot a aplikace Spring Boot s využitím Application Insights.

## <a name="using-spring-boot-15x"></a>Použití aplikace Spring spouštěcí 1,5 x
Do souboru pom.xml nebo build.gradle přidejte následující závislosti: 
* [Application Insights aplikace spring-boot-starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter)1.1.0-BETA nebo novější
* Micrometer Azure registru 1.1.0 nebo novější
* [Starší verze micrometer Spring](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 nebo vyšší (v tomto zpětné autoconfig kódu v rámci Spring).
* [Prostředek ApplicationInsights](app-insights-create-new-resource.md)

Kroky

1. Aktualizovat soubor pom.xml aplikace Spring Boot a přidejte do něj následující závislosti:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>1.1.0-BETA</version>
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
2. Aktualizujte soubor souboru application.properties nebo yml klíč instrumentace Application Insights pomocí následující vlastnost:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Sestavení aplikace a spustit
2. Výše uvedené by vám a pomocí předem agregovaných metrik automaticky shromažďovat do Azure monitoru. Podrobnosti o tom, jak doladit Application Insights Spring Boot starter najdete [souboru readme na Githubu](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Pomocí Spring 2.x

Do souboru pom.xml nebo build.gradle přidejte následující závislosti:

* Application Insights Spring boot starter 2.1.2 nebo novější
* Azure-spring spouštěcí metrics spouštěčů 2.0.7 nebo novější  
* [Prostředek služby Application Insights](app-insights-create-new-resource.md)

Kroky:

1. Aktualizovat soubor pom.xml aplikace Spring Boot a do něj přidejte následující závislost:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Aktualizujte soubor souboru application.properties nebo yml klíč instrumentace Application Insights pomocí následující vlastnost:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Sestavení aplikace a spustit
4. Výše uvedené by vám pomůžou s předem agregovaných metrik automaticky shromažďovat do Azure monitoru. Podrobnosti o tom, jak doladit Application Insights Spring Boot starter najdete [souboru readme na Githubu](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Výchozí metriky:

*    Automaticky nakonfiguruje metriky pro Tomcat, JVM, Logback metriky, Log4J metriky, dobu provozu metriky, metriky procesoru, FileDescriptorMetrics.
*    Například pokud je k dispozici v cestě třída netflix hystrix získáme i tyto metriky. 
*    Přidáním odpovídajících lusků může být k dispozici následující metriky. 
        - CacheMetrics (CaffineCache EhCache2, GuavaCache, HazelcaseCache, Jcache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - OkHttp3 metriky 
        - Kafka metriky 

 

Jak chcete-li vypnout automatické metriky kolekce: 
 
- Metriky JVM: 
    - Management.Metrics.binders.JVM.Enabled=false 
- Logback metriky: 
    - Management.Metrics.binders.logback.Enabled=false
- Metriky dostupnosti: 
    - Management.Metrics.binders.UpTime.Enabled=false 
- Metriky procesoru:
    -  Management.Metrics.binders.Processor.Enabled=false 
- FileDescriptorMetrics:
    - Management.Metrics.binders.Files.Enabled=false 
- Metriky Hystrix Pokud knihovny v cestě: 
    - Management.Metrics.binders.hystrix.Enabled=false 
- Metriky AspectJ Pokud knihovny v cestě: 
    - Spring.aop.Enabled=false 

> [!NOTE]
> Zadejte vlastnosti výše v souboru application.properties nebo application.yml aplikace Spring Boot

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Micrometer pomocí webové aplikace Spring Boot

Do souboru pom.xml nebo build.gradle přidejte následující závislosti:
 
* [Základní přehled aplikace 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.2.0) nebo novější
* [Application Insights webové 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/2.2.0) nebo novější
* [Registrace webového filtru](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* Micrometer Azure registru 1.1.0 nebo novější
* [Prostředek služby Application Insights](app-insights-create-new-resource.md)

Kroky:

1. Přidejte následující závislosti do souboru pom.xml nebo build.gradle:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web</artifactId>
            <version>2.2.0</version>
        </dependency
     ```

2. Vložit Insights.xml aplikace ve složce resources

    Servlet ukázkovou třídu (vysílá časovač metrika):

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

      Ukázka třídy konfigurace:

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

Další informace o metrikách, najdete v tématu [Micrometer dokumentaci](https://micrometer.io/docs/).

Další ukázkový kód pro vytvoření různých typů metrik najdete v[oficiální úložiště Micrometer Github](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Jak vytvořit vazbu kolekce další metriky

### <a name="springbootspring"></a>SpringBoot/Spring

Vytvořte položku bean příslušné kategorie metriky. Předpokládejme například, že potřebujeme kvajáva metriky mezipaměti:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Existuje několik metrik, které nejsou ve výchozím nastavení povolené, ale může být vázaný nad způsobem. Úplný seznam najdete v tématu [oficiální úložiště Micrometer Github](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Aplikace bez Spring
Přidejte následující kód vazby do konfiguračního souboru:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Další postup

* Další informace o Micrometer odkazovat na oficiální [Micrometer dokumentaci](https://micrometer.io/docs).
* Další informace o Spring na Azure najdete v oficiální [Spring v dokumentaci Azure](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable).
