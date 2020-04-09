---
title: Monitorování aplikací java v libovolném prostředí – Azure Monitor Application Insights
description: Monitorování výkonu aplikací pro aplikace Java spuštěné v libovolném prostředí bez instrumentace aplikace. Distribuované trasování a mapa aplikací.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 5a62be45320523ee0577d56eb557a4f87a58a1cc
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886853"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Java bezkódové aplikace monitorování Azure Monitor Application Insights - public preview

Java codeless application monitoring je o jednoduchosti - neexistují žádné změny kódu, agent Java může být povolen pouze prostřednictvím několika konfiguračních změn.

 Agent Java pracuje v jakémkoli prostředí a umožňuje sledovat všechny vaše java aplikace. Jinými slovy, ať už používáte java aplikace na virtuálních počítačích, místně, v AKS, ve Windows, Linuxu – na co si vzpomenete, agent Java 3.0 bude vaši aplikaci monitorovat.

Přidání sady Application Insights Java SDK do vaší aplikace již není nutné, protože agent 3.0 automaticky shromažďuje požadavky, závislosti a zaznamenává všechny samostatně.

Stále můžete odeslat vlastní telemetrie z vaší aplikace. Agent 3.0 bude sledovat a korelovat spolu se všemi automaticky odebrané telemetrie.

## <a name="quickstart"></a>Rychlý start

**1. Stáhněte si agenta**

Stáhnout [applicationinsights-agent-3.0.0-PREVIEW.2.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.2/applicationinsights-agent-3.0.0-PREVIEW.2.jar)

**2. Namiřte JVM na agenta**

Přidat `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.2.jar` do vaší aplikace JVM args

Typické JVM args patří `-Xmx512m` a `-XX:+UseG1GC`. Takže pokud víte, kam je přidat, pak už víte, kam to přidat.

Další pomoc s konfigurací jvm args aplikace naleznete v [tématu 3.0 Preview: Tipy pro aktualizaci vašeho JVM args](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Tips-for-updating-your-JVM-args).

**3. Najeďte agenta na prostředek Application Insights.**

Pokud ještě nemáte prostředek Application Insights, můžete vytvořit nový pomocí kroků v [průvodci vytvářením prostředků](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).
Vytvořte konfigurační soubor s názvem `ApplicationInsights.json` `applicationinsights-agent-3.0.0-PREVIEW.2.jar`a umístěte jej do stejného adresáře jako s následujícím obsahem:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Připojovací řetězec najdete v prostředku Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Připojovací řetězec Přehledy aplikací":::

**4. To je ono!**

Teď spusťte aplikaci a přejděte na svůj prostředek Application Insights na webu Azure Portal a podívejte se na data monitorování.

> [!NOTE]
> Může trvat několik minut, než se data monitorování zobrazí na portálu.


## <a name="configuration-options"></a>Možnosti konfigurace

V `ApplicationInsights.json` souboru můžete navíc nakonfigurovat:

* Název role cloudu
* Instance role cloudu
* Zachycení protokolu aplikací
* Metriky JMX
* Mikrometr
* Prezenční signál
* Vzorkování
* HTTP Proxy
* Vlastní diagnostika

Podrobnosti naleznete v náhledu [3.0: Možnosti konfigurace](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Configuration-Options).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Automaticky shromažďované požadavky, závislosti, protokoly a metriky

### <a name="requests"></a>Žádosti

* JMS Spotřebitelé
* Kafka Spotřebitelé
* Netty/WebFlux
* Serva
* Jarní plánování

### <a name="dependencies-with-distributed-trace-propagation"></a>Závislosti s distribuovaným šířením trasování

* Klient Apache HttpClient a httpasynchronní klient
* gRPC
* java.net.HttpURLPřipojení
* JMS
* Kafka
* Klient Netty
* OkHttp

### <a name="other-dependencies"></a>Další závislosti

* Cassandra
* JDBC
* MongoDB (asynchronní a synchronizované)
* Redis (salát a Jediové)

### <a name="logs"></a>Protokoly

* java.util.logging
* Protokol4j
* SLF4J/Zpětná přihlašování

### <a name="metrics"></a>Metriky

* Mikrometr
* Metriky JMX

## <a name="sending-custom-telemetry-from-your-application"></a>Odesílání vlastní telemetrie z vaší aplikace

Naším cílem v 3.0+ je umožnit odesílání vlastní telemetrie pomocí standardních api.

Podporujeme Micrometer, OpenTelemetric API a populární logovací rámce. Application Insights Java 3.0 automaticky zachytí telemetrii a koreluje ji spolu se všemi automaticky sebrané telemetrie.

Z tohoto důvodu neplánujeme vydat sdk s Application Insights 3.0 v tuto chvíli.

Application Insights Java 3.0 již naslouchá telemetrii, která je odeslána do sady Application Insights Java SDK 2.x. Tato funkce je důležitou součástí upgradu příběhu pro stávající uživatele 2.x a vyplňuje důležitou mezeru v naší vlastní telemetrické podpoře, dokud OpenTelemetry API je GA.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Odesílání vlastní telemetrie pomocí application insights java sdcharty 2.x

Přidejte `applicationinsights-core-2.6.0.jar` do aplikace (všechny verze 2.x jsou podporovány Application Insights Java 3.0, ale stojí za to použít nejnovější, pokud máte na výběr):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Vytvořte telemetrický klient:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

a použít pro odesílání vlastní telemetrie.

### <a name="events"></a>Události

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Metriky

Metrickou telemetrii můžete odesílat pomocí [mikrometru](https://micrometer.io):

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

Nebo můžete také použít Application Insights Java SDK 2.x:

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
Můžete odeslat vlastní telemetrická data prostřednictvím své oblíbené architektury protokolování.

Nebo můžete také použít Application Insights Java SDK 2.x:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Výjimky
Můžete odeslat vlastní výjimku telemetrie prostřednictvím své oblíbené rozhraní protokolování.

Nebo můžete také použít Application Insights Java SDK 2.x:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Upgrade z aplikace Insights Java SDK 2.x

Pokud už ve své aplikaci používáte application insights Java SDK 2.x, není nutné ji odebírat. Agent Java 3.0 ji detekuje a zachytí a koreluje všechny vlastní telemetrie, které odesíláte prostřednictvím sady Java SDK 2.x, a zároveň potlačí všechny automatické vzpomínky prováděné sadou Java SDK 2.x, aby se zabránilo duplicitnímu zachycení.

> [!NOTE]
> Poznámka: Java SDK 2.x TelemetryInitializers a TelemetryProcesors se nespustí při použití agenta 3.0.
