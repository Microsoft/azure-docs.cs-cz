---
title: Azure Monitor Application Insights Java
description: Monitorování výkonu aplikací pro aplikace v jazyce Java spuštěné v jakémkoli prostředí bez nutnosti změny kódu. Distribuované trasování a mapa aplikací
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 3f22e165fe4a3f86ecce8b1e307b19fae0eeac81
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812043"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Azure Monitor monitorování aplikací s kódováním kódu Java Application Insights

Monitorování aplikací bez kódu Java je vše o jednoduchosti – neexistují žádné změny kódu, agent Java může být povolený jenom v několika změnách konfigurace.

 Agent Java funguje v jakémkoli prostředí a umožňuje monitorovat všechny aplikace v jazyce Java. Jinými slovy, bez ohledu na to, jestli máte aplikace Java spuštěné na virtuálních počítačích, místně v AKS, v systému Windows, Linux – pojmenujte ji agent Java 3,0, který bude vaši aplikaci monitorovat.

Přidání Application Insights Java SDK do vaší aplikace už není potřeba, protože agent 3,0 automaticky shromažďuje požadavky, závislosti a protokoly sama o sobě.

Z aplikace můžete stále odesílat vlastní telemetrie. Agent 3,0 bude sledovat a koreluje spolu se všemi automaticky shromážděnými telemetrie.

Agent 3,0 podporuje jazyk Java 8 a vyšší.

## <a name="quickstart"></a>Rychlé zprovoznění

**1. Stáhněte agenta.**

> [!WARNING]
> **Pokud upgradujete z verze 3,0 Preview**
>
> Přečtěte si pečlivě všechny [Možnosti konfigurace](./java-standalone-config.md) , protože se úplně změnila struktura JSON, kromě samotného názvu souboru, který všechno byl malý.

Stáhnout [ApplicationInsights-agent-3.0.3. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.3/applicationinsights-agent-3.0.3.jar)

**2. Nasměrujte JVM na agenta.**

Přidat `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` do ARGUMENTŮ JVM vaší aplikace

Typické argumenty JVM zahrnují `-Xmx512m` a `-XX:+UseG1GC` . Takže pokud víte, kam je přidat, pak už víte, kam je přidat.

Další nápovědu ke konfiguraci argumentů JVM vaší aplikace najdete v [tipech pro aktualizaci ARGUMENTŮ JVM](./java-standalone-arguments.md).

**3. Nasměrujte agenta na prostředek Application Insights**

Pokud ještě prostředek Application Insights nemáte, můžete ho vytvořit pomocí postupu v [příručce pro vytváření prostředků](./create-new-resource.md).

Najeďte agentem na prostředek Application Insights, a to nastavením proměnné prostředí:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Nebo vytvořením konfiguračního souboru s názvem `applicationinsights.json` a jeho umístěním do stejného adresáře jako `applicationinsights-agent-3.0.3.jar` s následujícím obsahem:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Připojovací řetězec najdete v prostředku Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Připojovací řetězec Application Insights":::

**4. to je!**

Nyní spusťte aplikaci a přejděte do svého prostředku Application Insights v Azure Portal, aby se zobrazila data monitorování.

> [!NOTE]
> Zobrazení dat monitorování na portálu může trvat několik minut.


## <a name="configuration-options"></a>Možnosti konfigurace

V `applicationinsights.json` souboru můžete také nakonfigurovat:

* Název cloudové role
* Instance cloudové role
* Vzorkování
* JMX metriky
* Vlastní rozměry
* Procesory telemetrie (Preview)
* Automaticky shromážděné protokolování
* Automaticky shromážděná metrika mikroměřiče (včetně metriky pohánějícího spouštěcího zařízení)
* Tep
* Proxy server HTTP
* Samoobslužná Diagnostika

Úplné podrobnosti najdete v tématu [Možnosti konfigurace](./java-standalone-config.md) .

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>Automaticky shromážděné požadavky, závislosti, protokoly a metriky

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

### <a name="azure-sdks-preview"></a>Sady SDK Azure (Preview)

Pokud chcete povolit tuto funkci ve verzi Preview a zachytit telemetrii vydanou těmito sadami Azure SDK, Prohlédněte si [Možnosti konfigurace](./java-standalone-config.md#auto-collected-azure-sdk-telemetry-preview) :

* [Konfigurace aplikace](https://docs.microsoft.com/java/api/overview/azure/data-appconfiguration-readme) 1.1.10 +
* [Kognitivní hledání](https://docs.microsoft.com/java/api/overview/azure/search-documents-readme) 11.3.0 +
* [Komunikační chat](https://docs.microsoft.com/java/api/overview/azure/communication-chat-readme) 1.0.0 +
* [Komunikace – společné](https://docs.microsoft.com/java/api/overview/azure/communication-common-readme) 1.0.0 +
* [Komunikace identity](https://docs.microsoft.com/java/api/overview/azure/communication-identity-readme) 1.0.0 +
* [Komunikace SMS](https://docs.microsoft.com/java/api/overview/azure/communication-sms-readme) 1.0.0 +
* [Cosmos DB](https://docs.microsoft.com/java/api/overview/azure/cosmos-readme) 4.13.0 +
* [Event Grid](https://docs.microsoft.com/java/api/overview/azure/messaging-eventgrid-readme) 4.0.0 +
* [Event Hubs](https://docs.microsoft.com/java/api/overview/azure/messaging-eventhubs-readme) 5.6.0 +
* [Event Hubs – Azure Blob Storage Checkpoint Store](https://docs.microsoft.com/java/api/overview/azure/messaging-eventhubs-checkpointstore-blob-readme) 1.5.1 +
* 3.0.6 a [rozpoznávání formulářů](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme)
* [Identita](https://docs.microsoft.com/java/api/overview/azure/identity-readme) 1.2.4 +
* [Key Vault – certifikáty](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-certificates-readme) 4.1.6 +
* [Key Vault klíče](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-keys-readme) 4.2.6 +
* [Key Vault – tajné klíče](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-secrets-readme) 4.2.6 +
* [Service Bus](https://docs.microsoft.com/java/api/overview/azure/messaging-servicebus-readme) 7.1.0 +
* [Analýza textu](https://docs.microsoft.com/java/api/overview/azure/ai-textanalytics-readme) 5.0.4 +

[//]: # "výše uvedené názvy a odkazy vyřazené z https://azure.github.io/azure-sdk/releases/latest/java.html"
[//]: # "a synchronizuje verze ručně s nejstarší verzí v Maven centrálně postavené na Azure-Core 1.14.0"
[//]: # ""
[//]: # "var Table = Document. querySelector (' #tg-SB-Content > div > Table ')"
[//]: # "var str = ' '"
[//]: # "pro (var i = 1, řádek; řádek = Table. Rows [i]; i + +) {"
[//]: # "  var Name = řádek. Cells [0]. getElementsByTagName (' div ') [0]. textContent. trim ()"
[//]: # "  var stableRow = řádek. Cells [1]"
[//]: # "  var versionBadge = stableRow. querySelector ('. BADGE ')"
[//]: # "  if (! versionBadge) {"
[//]: # "    pokraeovat"
[//]: # "  }"
[//]: # "  var verze = versionBadge. textContent. trim ()"
[//]: # "  var link = stableRow. querySelectorAll (' a ') [2]. href"
[//]: # "  str + = ' * [' + název + '] (' + Link + ') ' + Version"
[//]: # "}"
[//]: # "Console. log (str)"

## <a name="send-custom-telemetry-from-your-application"></a>Odeslání vlastní telemetrie z vaší aplikace

Naším cílem v 3.0 + je umožnění odesílání vlastní telemetrie pomocí standardních rozhraní API.

Podporuje mikroměřiče, oblíbená rozhraní protokolování a zatím Application Insights Java 2. x SDK.
Application Insights Java 3,0 automaticky zachycuje telemetrii poslaná pomocí těchto rozhraní API a koreluje ji s automatickou shromážděnou telemetrie.

### <a name="supported-custom-telemetry"></a>Podporovaná vlastní telemetrie

Následující tabulka představuje aktuálně podporované vlastní typy telemetrie, které můžete povolit pro doplnění agenta Java 3,0. Pro sumarizaci jsou vlastní metriky podporovány prostřednictvím mikroměřiče, vlastní výjimky a trasování lze povolit prostřednictvím protokolovacích rozhraní a jakýkoli typ vlastní telemetrie je podporován prostřednictvím [Application Insights Java 2. x SDK](#send-custom-telemetry-using-the-2x-sdk).

|                     | Mikrometr | Log4j, logback, červenec | 2. x SDK |
|---------------------|------------|---------------------|---------|
| **Vlastní události**   |            |                     |  Yes    |
| **Vlastní metriky**  |  Yes       |                     |  Yes    |
| **Závislosti**    |            |                     |  Yes    |
| **Výjimky**      |            |  Yes                |  Yes    |
| **Page Views**      |            |                     |  Yes    |
| **Žádosti**        |            |                     |  Yes    |
| **Trasování**          |            |  Yes                |  Yes    |

V tuto chvíli neplánujeme vydání sady SDK pomocí Application Insights 3,0.

Application Insights Java 3,0 již naslouchá telemetrie, která je odeslána do sady Application Insights Java 2. x SDK. Tato funkce je důležitou součástí článku upgradu pro existující uživatele 2. x a vyplní důležitou mezeru v naší podpoře vlastní telemetrie, dokud není rozhraní OpenTelemetry API GA.

### <a name="send-custom-metrics-using-micrometer"></a>Odesílání vlastních metrik pomocí mikroměřiče

Přidat mikroměřič do aplikace:

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Použití [globálního registru](https://micrometer.io/docs/concepts#_global_registry) mikroměřiče k vytvoření měřiče:

```java
static final Counter counter = Metrics.counter("test_counter");
```

a používá se pro záznam metrik:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Posílání vlastních trasování a výjimek pomocí oblíbeného protokolovacího rozhraní

Log4j, Logback a Java. util. protokolování je automaticky instrumentované a protokolování se provádí pomocí těchto protokolovacích rozhraní, které se automaticky shromáždí jako telemetrie trasování a výjimek.

Ve výchozím nastavení je protokolování shromažďováno pouze v případě, že je protokolování provedeno na úrovni informací nebo výše.
Postup změny této úrovně najdete v [možnostech konfigurace](./java-standalone-config.md#auto-collected-logging) .

Pokud chcete k protokolům připojovat vlastní dimenze, můžete použít [Log4j 1,2 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html), [log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)nebo [Logback MDC](http://logback.qos.ch/manual/mdc.html)a Application Insights Java 3,0 automaticky zachytává tyto vlastnosti MDC jako vlastní rozměry na vaší platformě trasování a telemetrie výjimek.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>Odeslání vlastní telemetrie pomocí sady 2. x SDK

Přidejte `applicationinsights-core-2.6.2.jar` do své aplikace (všechny 2. x verze jsou podporovány Application Insights Java 3,0, ale Využijte možnost nejnovější, pokud máte možnost použít):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.2</version>
</dependency>
```

Vytvořte TelemetryClient:

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

a použít ho k odeslání vlastní telemetrie:

##### <a name="events"></a>Události

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>Metriky

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>Závislosti

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>Protokoly

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>Výjimky

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>Přidání vlastních dimenzí žádosti pomocí sady 2. x SDK

> [!NOTE]
> Tato funkce je pouze v 3.0.2 a novějších.

Přidejte `applicationinsights-web-2.6.2.jar` do své aplikace (všechny 2. x verze jsou podporovány Application Insights Java 3,0, ale Využijte možnost nejnovější, pokud máte možnost použít):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

a přidejte vlastní dimenze do kódu:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>Nastavení telemetrie požadavků user_Id pomocí sady 2. x SDK

> [!NOTE]
> Tato funkce je pouze v 3.0.2 a novějších.

Přidejte `applicationinsights-web-2.6.2.jar` do své aplikace (všechny 2. x verze jsou podporovány Application Insights Java 3,0, ale Využijte možnost nejnovější, pokud máte možnost použít):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

a nastavte `user_Id` v kódu:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>Přepsat název telemetrie žádosti pomocí sady 2. x SDK

> [!NOTE]
> Tato funkce je pouze v 3.0.2 a novějších.

Přidejte `applicationinsights-web-2.6.2.jar` do své aplikace (všechny 2. x verze jsou podporovány Application Insights Java 3,0, ale Využijte možnost nejnovější, pokud máte možnost použít):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

a nastavte název v kódu:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.setName("myname");
```

### <a name="get-the-request-telemetry-id-and-the-operation-id-using-the-2x-sdk"></a>Získání ID telemetrie žádosti a ID operace pomocí sady 2. x SDK

> [!NOTE]
> Tato funkce je pouze v 3.0.3 a novějších.

Přidejte `applicationinsights-web-2.6.2.jar` do své aplikace (všechny 2. x verze jsou podporovány Application Insights Java 3,0, ale Využijte možnost nejnovější, pokud máte možnost použít):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.2</version>
</dependency>
```

a Získejte ID telemetrie žádosti a ID operace ve vašem kódu:

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
String requestId = requestTelemetry.getId();
String operationId = requestTelemetry.getContext().getOperation().getId();
```
