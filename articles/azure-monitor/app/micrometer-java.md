---
title: Použití mikroměřiče s Azure Application Insights Java SDK
description: Podrobný návod k použití mikroměřiče s aplikacemi Application Insights pružinové spouštěcí a nepružinové spouštěcí aplikace.
ms.topic: conceptual
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.date: 11/01/2018
ms.openlocfilehash: 8dba7280f6abd6026fabdde500dc76b73129d557
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589760"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk-not-recommended"></a>Použití mikroměřiče s Azure Application Insights Java SDK (nedoporučuje se)

> [!IMPORTANT]
> Přístup popsaný v tomto dokumentu se už nedoporučuje.
> 
> Doporučený postup pro monitorování aplikací Java je použití automatické instrumentace beze změny kódu. Telemetrie mikroměření se automaticky shromáždila pomocí Application Insights agenta Java 3,0 – postupujte podle pokynů pro [Application Insights agenta java 3,0](./java-in-process-agent.md).

> [!NOTE]
> Sada Application Insights Java SDK nepodporuje místo toho [Application Insights agenta java 3,0](./java-in-process-agent.md) . 
>
> V [Application Insights agenta Java 3,0](./java-on-premises.md) , který nevyžaduje instrumentaci, se podporuje webtokem i mikroměřiče. 

Měření míry monitorování aplikací pro mikroměřiče pro kód aplikace založené na JVM a umožňuje exportovat data do oblíbených monitorovacích systémů. V tomto článku se dozvíte, jak používat mikroměřiče s Application Insights pro aplikace pro spouštění pružinového spouštění i pro aplikace bez pružiny.

## <a name="using-spring-boot-15x"></a>Použití pružinového spuštění 1.5 x
Do pom.xml nebo sestavení souboru. Gradle přidejte následující závislosti: 
* [Application Insights jaře-Boot-Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter) 2.5.0 nebo novější
* 1.1.0 nebo novější v registru mikroměřiče Azure
* [Pružina mikroměřiče starší verze](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 nebo vyšší (Tato znovu nasměruje kód pro automatické nastavování v rozhraní pružiny).
* [Prostředek ApplicationInsights](./create-new-resource.md)

Postup

1. Aktualizujte soubor pom.xml vaší aplikace pro spouštění pružin a přidejte do ní následující závislosti:

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
2. Aktualizujte soubor Application. Properties nebo YML pomocí klíče instrumentace Application Insights pomocí následující vlastnosti:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Sestavení aplikace a spuštění
2. Výše uvedená zpráva by vám měla začít pracovat s předem agregovanými metrikami automaticky shromážděnými do Azure Monitor. Podrobné informace o tom, jak vyladit Application Insights jaře Boot Starter, najdete v [souboru Readme na GitHubu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Použití pružiny 2. x

Do pom.xml nebo sestavení souboru. Gradle přidejte následující závislosti:

* Application Insights pružiny-Boot-Starter 2.1.2 nebo vyšší
* Azure-jaře-Boot – metriky – starts 2.0.7 nebo novější
* [Prostředek Application Insights](./create-new-resource.md)

Kroky:

1. Aktualizujte soubor pom.xml vaší aplikace pro spouštění pružin a přidejte do ní následující závislost:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Aktualizujte soubor Application. Properties nebo YML pomocí klíče instrumentace Application Insights pomocí následující vlastnosti:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Sestavení aplikace a spuštění
4. Výše uvedený postup by měl být spuštěn s předem agregovanými metrikami automaticky shromážděnými do Azure Monitor. Podrobné informace o tom, jak vyladit Application Insights jaře Boot Starter, najdete v [souboru Readme na GitHubu](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Výchozí metriky:

*    Automaticky konfigurované metriky pro metriky Tomcat, JVM, Logback, metriky Log4J, metriky provozu, metriky procesoru, FileDescriptorMetrics.
*    Například pokud je Netflix hystrix k dispozici na cestě třídy, získáme také tyto metriky. 
*    Následující metriky mohou být k dispozici, když přidáváte příslušné fazole. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcastCache, JCache)
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - OkHttp3 metriky 
        - Kafka metriky 



Vypnutí automatické shromažďování metrik: 

- JVM metriky: 
    - Management. Metrics. pojiv. JVM. Enabled = FALSE 
- Logback metriky: 
    - Management. Metrics. pojiv. logback. Enabled = FALSE
- Metriky provozu: 
    - Management. Metrics. propojuje.-time. Enabled = FALSE 
- Metriky procesoru:
    -  Management. Metrics. pojivs. procesor. Enabled = FALSE 
- FileDescriptorMetrics:
    - Management. Metrics. propojujed. Files. Enabled = FALSE 
- Hystrix metriky v případě knihovny pro cestu k cestě: 
    - Management. Metrics. pojiv. hystrix. Enabled = FALSE 
- AspectJ metriky v případě knihovny pro cestu k cestě: 
    - pružina. aop. Enabled = FALSE 

> [!NOTE]
> Zadejte vlastnosti uvedené výše v souboru Application. Properties nebo Application. yml vaší aplikace pro spouštění pružiny.

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Použití mikroměřiče u webových aplikací, které nejsou ve jarním spuštění

Do pom.xml nebo sestavení souboru. Gradle přidejte následující závislosti:

* Application Insights 2.5.0 Web auto nebo novější
* 1.1.0 nebo novější v registru mikroměřiče Azure
* [Prostředek Application Insights](./create-new-resource.md)

Kroky:

1. Do pom.xml nebo sestavení souboru. Gradle přidejte následující závislosti:

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

2. Umístit `ApplicationInsights.xml` soubor do složky Resources:

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

3. Ukázková třída servlet (emituje metriku časovače):

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

Další informace o metrikách najdete v [dokumentaci k mikroměřiči](https://micrometer.io/docs/).

Další ukázkový kód pro vytvoření různých typů metrik najdete v[oficiálním úložišti GitHub mikroměřiče](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Vytvoření vazby dalších kolekcí metrik

### <a name="springbootspring"></a>SpringBoot/pružina

Vytvořte Bob příslušné kategorie metriky. Řekněme například, že potřebujeme metriky guava cache:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
K dispozici je několik metrik, které ve výchozím nastavení nejsou povoleny, ale mohou být vázány výše uvedeným způsobem. Úplný seznam najdete [v oficiálním úložišti GitHubu pro mikroměřiče](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Aplikace bez pružiny
Do konfiguračního souboru přidejte následující kód vazby:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Další kroky

* Další informace o mikroměřiči najdete v oficiální [dokumentaci k mikroměřiči](https://micrometer.io/docs).
* Další informace o jaře v Azure najdete v [dokumentaci k oficiální jaře na platformě Azure](/java/azure/spring-framework/).
