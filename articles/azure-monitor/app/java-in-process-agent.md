---
title: Monitorování aplikací Java v jakémkoli prostředí – Azure Monitor Application Insights
description: Monitorování výkonu aplikací pro aplikace Java spuštěné v jakémkoli prostředí bez instrumentace aplikace Distribuované trasování a mapa aplikací
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 1182813c0b79d43c2c264482629ad97f23683a49
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215276"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Monitorování aplikací nezaložených na kódu Java Azure Monitor Application Insights – Public Preview

Monitorování aplikací bez kódu Java je vše o jednoduchosti – neexistují žádné změny kódu, agent Java může být povolený jenom v několika změnách konfigurace.

 Agent Java funguje v jakémkoli prostředí a umožňuje monitorovat všechny aplikace v jazyce Java. Jinými slovy, bez ohledu na to, jestli máte aplikace Java spuštěné na virtuálních počítačích, místně v AKS, v systému Windows, Linux – pojmenujte ji agent Java 3,0, který bude vaši aplikaci monitorovat.

Přidání sady Application Insights Java SDK do vaší aplikace už není potřeba, protože agent 3,0 autoshromáždí požadavky, závislosti a protokoly sám o sobě.

Z aplikace můžete stále odesílat vlastní telemetrie. Agent 3,0 bude sledovat a koreluje spolu se všemi všemi shromažďovanými telemetriemi.

Agent 3,0 podporuje jazyk Java 8 a vyšší.

## <a name="quickstart"></a>Rychlé zprovoznění

**1. Stáhněte agenta.**

Stáhnout [ApplicationInsights-agent-3.0.0-Preview. 7. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.7/applicationinsights-agent-3.0.0-PREVIEW.7.jar)

**2. Nasměrujte JVM na agenta.**

Přidat `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.7.jar` do ARGUMENTŮ JVM vaší aplikace

Typické argumenty JVM zahrnují `-Xmx512m` a `-XX:+UseG1GC` . Takže pokud víte, kam je přidat, pak už víte, kam je přidat.

Další nápovědu ke konfiguraci argumentů JVM vaší aplikace najdete v článku [3,0 Preview: Tipy pro aktualizaci ARGUMENTŮ JVM](./java-standalone-arguments.md).

**3. Nasměrujte agenta na prostředek Application Insights**

Pokud ještě prostředek Application Insights nemáte, můžete ho vytvořit pomocí postupu v [příručce pro vytváření prostředků](./create-new-resource.md).

Najeďte agentem na prostředek Application Insights, a to nastavením proměnné prostředí:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

Nebo vytvořením konfiguračního souboru s názvem `ApplicationInsights.json` a jeho umístěním do stejného adresáře jako `applicationinsights-agent-3.0.0-PREVIEW.7.jar` s následujícím obsahem:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Připojovací řetězec najdete v prostředku Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Připojovací řetězec Application Insights":::

**4. to je!**

Nyní spusťte aplikaci a přejděte do svého prostředku Application Insights v Azure Portal, aby se zobrazila data monitorování.

> [!NOTE]
> Zobrazení dat monitorování na portálu může trvat několik minut.


## <a name="configuration-options"></a>Možnosti konfigurace

V `ApplicationInsights.json` souboru můžete také nakonfigurovat:

* Název cloudové role
* Instance cloudové role
* Zachycení protokolu aplikace
* JMX metriky
* Mikrometr
* Tep
* Vzorkování
* Proxy server HTTP
* Automatická diagnostika

Podrobnosti najdete v článku [3,0 Public Preview: možnosti konfigurace](./java-standalone-config.md).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Autoshromáždit požadavky, závislosti, protokoly a metriky

### <a name="requests"></a>Žádosti

* JMS spotřebitelé
* Kafka spotřebitelé
* Síťovina/webtokem
* Servletů
* Plánování pružiny

### <a name="dependencies-with-distributed-trace-propagation"></a>Závislosti s šířením distribuovaného trasování

* Apache HttpClient a HttpAsyncClient
* gRPC
* Java. NET. HttpURLConnection
* JMS
* Kafka
* Klient síťoviny
* OkHttp

### <a name="other-dependencies"></a>Další závislosti

* Cassandra
* JDBC
* MongoDB (asynchronní a synchronizace)
* Redis (Lettuce a Jedis)

### <a name="logs"></a>Protokoly

* Java. util. Logging
* Log4j (včetně vlastností MDC)
* SLF4J/Logback (včetně vlastností MDC)

### <a name="metrics"></a>Metriky

* Mikroměřič (včetně metriky pohánějícího spouštěcího zařízení)
* JMX metriky

## <a name="sending-custom-telemetry-from-your-application"></a>Odesílá se vlastní telemetrie z vaší aplikace.

Naším cílem v 3.0 + je umožnění odesílání vlastní telemetrie pomocí standardních rozhraní API.

Podporujeme mikroměřiče, rozhraní OpenTelemetry API a oblíbená rozhraní protokolování. Application Insights Java 3,0 automaticky zachytí telemetrii a koreluje je spolu se všemi automaticky shromážděnou telemetrie.

### <a name="supported-custom-telemetry"></a>Podporovaná vlastní telemetrie

Následující tabulka představuje aktuálně podporované vlastní typy telemetrie, které můžete povolit pro doplnění agenta Java 3,0. Pro sumarizaci jsou vlastní metriky podporovány prostřednictvím mikroměřiče, vlastní výjimky a trasování lze povolit prostřednictvím protokolovacích rozhraní a jakýkoli typ vlastní telemetrie je podporován prostřednictvím [Application Insights Java 2. x SDK](#sending-custom-telemetry-using-application-insights-java-sdk-2x). 

|                     | Mikrometr | Log4j, logback, červenec | 2. x SDK |
|---------------------|------------|---------------------|---------|
| **Vlastní události**   |            |                     |  Ano    |
| **Vlastní metriky**  |  Ano       |                     |  Ano    |
| **Závislosti**    |            |                     |  Ano    |
| **Výjimky**      |            |  Ano                |  Ano    |
| **Page Views**      |            |                     |  Ano    |
| **Žádosti**        |            |                     |  Ano    |
| **Trasování**          |            |  Ano                |  Ano    |

V tuto chvíli neplánujeme vydání sady SDK pomocí Application Insights 3,0.

Application Insights Java 3,0 již naslouchá telemetrie, která je odeslána do Application Insights Java SDK 2. x. Tato funkce je důležitou součástí článku upgradu pro existující uživatele 2. x a vyplní důležitou mezeru v naší podpoře vlastní telemetrie, dokud není rozhraní OpenTelemetry API GA.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Odesílání vlastní telemetrie pomocí Application Insights Java SDK 2. x

Přidejte `applicationinsights-core-2.6.0.jar` do své aplikace (všechny 2. x verze jsou podporovány Application Insights Java 3,0, ale Využijte možnost nejnovější, pokud máte možnost použít):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Vytvořte TelemetryClient:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

a používají se k odesílání vlastní telemetrie.

### <a name="events"></a>Události

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Metriky

Telemetrii metriky můžete poslat pomocí [mikroměřiče](https://micrometer.io):

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

Můžete také použít Application Insights Java SDK 2. x:

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>Závislosti

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>Protokoly
Vlastní telemetrii protokolu můžete odeslat prostřednictvím oblíbeného protokolovacího rozhraní.

Můžete také použít Application Insights Java SDK 2. x:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Výjimky
Můžete odesílat vlastní telemetrii výjimek prostřednictvím oblíbeného protokolovacího rozhraní.

Můžete také použít Application Insights Java SDK 2. x:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Upgrade z Application Insights Java SDK 2. x

Pokud v aplikaci již používáte Application Insights Java SDK 2. x, není nutné ji odebrat. Agent Java 3,0 ho detekuje a zachytí a koreluje se všemi vlastními telemetriemi, které posíláte prostřednictvím sady Java SDK 2. x, a zároveň potlačí všechny kolekce, které provádí Java SDK 2. x, aby se zabránilo duplicitnímu zachycení.

Pokud jste používali agenta Application Insights 2. x, je nutné odebrat `-javaagent:` JVM arg, která odkazovala na agenta 2. x.

> [!NOTE]
> Poznámka: Java SDK 2. x TelemetryInitializers a TelemetryProcessors se při použití agenta 3,0 nespustí.
