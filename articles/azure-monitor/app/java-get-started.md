---
title: 'Rychlý Start: Analýza webových aplikací Java pomocí Azure Application Insights'
description: 'Sledování výkonu webových aplikací Java pomocí Application Insights '
ms.topic: conceptual
author: lgayhardt
ms.custom: devx-track-java
ms.author: lagayhar
ms.date: 05/24/2019
ms.openlocfilehash: 12497d3ac86888ed861e8d5f655f45c8cbe4b6e3
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996158"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Rychlý Start: Začínáme s Application Insights ve webovém projektu Java


> [!IMPORTANT]
> Doporučený postup pro monitorování aplikací Java je použití automatické instrumentace beze změny kódu. Postupujte prosím podle pokynů pro [Application Insights agenta Java 3,0](./java-in-process-agent.md).

V tomto rychlém startu použijete Application Insights SDK k instrumentování požadavků, sledování závislostí a shromažďování čítačů výkonu, diagnostikování problémů s výkonem a výjimek a psaní kódu ke sledování toho, co uživatelé s vaší aplikací dělají.

Application Insights představují rozšiřitelnou analytickou službu, která webovým vývojářům pomůže pochopit výkon a využití živých aplikací. Application Insights podporuje aplikace v Javě spuštěné v systému Linux, Unix nebo Windows.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Funkční aplikace Java.

## <a name="get-an-application-insights-instrumentation-key"></a>Získejte klíč instrumentace Application Insights

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Na webu Azure Portalvytvořte prostředek Application Insights. Nastavte typ aplikace na webovou aplikaci Java.

3. Najděte klíč instrumentace nového prostředku. Tento klíč budete muset za chvíli vložit do projektu kódu.

    ![V přehledu nového prostředku klikněte na tlačítko Vlastnosti a zkopírujte klíč instrumentace](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>Do projektu přidejte Application Insights SDK pro jazyk Java

*Vyberte typ projektu.*

# <a name="maven"></a>[Maven](#tab/maven)

Pokud je váš projekt již nastaven na použití Maven pro sestavení, slučte následující kód do souboru *pom.xml* .

Pak obnovte závislosti projektu k získání stažených binárních souborů.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

Pokud je váš projekt již nastaven na použití Gradle pro sestavení, slučte následující kód do souboru *Build. Gradle* .

Pak obnovte závislosti projektu k získání stažených binárních souborů.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

# <a name="other-types"></a>[Další typy](#tab/other)

Stáhněte si [nejnovější verzi](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) a zkopírujte do svého projektu potřebné soubory, přičemž nahraďte jejich starší verze.

---

### <a name="questions"></a>Dotazy
* *Jaký je vztah mezi `-web-auto` `-web` komponentami a a `-core` ?*
  * `applicationinsights-web-auto` poskytuje metriky, které sledují počty požadavků HTTP servlet a doby odezvy, automatickým registrací filtru Application Insights servlet za běhu.
  * `applicationinsights-web` také poskytuje metriky, které sledují počty požadavků HTTP servlet a doby odezvy, ale vyžaduje ruční registraci filtru Application Insights servlet ve vaší aplikaci.
  * `applicationinsights-core` poskytuje pouze úplné rozhraní API, například pokud vaše aplikace není založená na servlet.
  
* *Jak mám aktualizovat sadu SDK na nejnovější verzi?*
  * Pokud používáte Gradle nebo Maven...
    * Aktualizujte soubor sestavení tak, aby určoval nejnovější verzi.
  * Pokud provádíte ruční správu závislostí...
    * Stáhněte si poslední [Application Insights SDK pro jazyk Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) a nahraďte staré. Změny jsou popsány v [poznámkách k verzi sady SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="add-an-applicationinsightsxml-file"></a>Přidat soubor *ApplicationInsights.xml*
Přidejte *ApplicationInsights.xml* do složky prostředků v projektu nebo se ujistěte, že je přidána do cesty třídy nasazení vašeho projektu. Zkopírujte do něj následující kód XML.

Nahraďte klíč instrumentace ten, který jste získali z Azure Portal.

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

V případě potřeby může být konfigurační soubor v jakémkoli umístění přístupném pro vaši aplikaci.  Vlastnost System `-Dapplicationinsights.configurationDirectory` Určuje adresář, který obsahuje *ApplicationInsights.xml*. Například konfigurační soubor umístěný v cestě `E:\myconfigs\appinsights\ApplicationInsights.xml` se nakonfiguruje pomocí vlastnosti `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* Klíč instrumentace se zasílá společně s každou položkou telemetrie a říká službě Application Insights, aby ho zobrazila v prostředku.
* Požadavek komponenty HTTP je volitelný. Automaticky odesílá telemetrii týkající se žádostí a časů odezvy na portál.
* Korelace událostí je doplněk komponenty požadavku HTTP. Každý požadavek přijatý serverem přiřadí identifikátor. Pak tento identifikátor přidá jako vlastnost do každé položky telemetrie jako vlastnost ' Operation.Id '. Umožňuje korelovat telemetrii související s každou žádostí nastavením filtru v [diagnostickém vyhledávání][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Alternativní způsoby nastavení klíče instrumentace
Application Insights SDK hledá klíče v tomto pořadí:

1. Systémová vlastnost:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Proměnná prostředí: APPINSIGHTS_INSTRUMENTATIONKEY
3. Konfigurační soubor: *ApplicationInsights.xml*

Můžete ho taky [nastavit v kódu](./api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Přidat agenta

[Nainstalujte agenta Java](java-agent.md) , abyste mohli zachytit odchozí volání http, dotazy JDBC, protokolování aplikací a lepší pojmenovávání operací.

## <a name="run-your-application"></a>Spusťte aplikaci
Buď ji spusťte v režimu ladění na vývojovém počítači, nebo publikujte na serveru.

## <a name="view-your-telemetry-in-application-insights"></a>Zobrazte telemetrii ve službě Application Insights
Vraťte se do prostředku Application Insights na web [Microsoft Azure Portal](https://portal.azure.com).

Data požadavků HTTP se zobrazí v okně přehledu. (Pokud zde nejsou, počkejte několik sekund a pak klikněte na tlačítko Aktualizovat.)

![Snímek obrazovky s přehledem ukázkových dat](./media/java-get-started/overview-graphs.png)

[Další informace o metrikách.][metrics]

Proklikejte se prostřednictvím jakékoli grafu pro zobrazení podrobnějších agregovaných metrik.

![Podokno selhání Application Insights s grafy](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Data instance
Proklikejte se jednotlivými typy konkrétního požadavku pro zobrazení jednotlivých instancí.

![Přejít k určitému ukázkovému zobrazení](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analýzy: účinný dotazovací jazyk
Jak shromažďujete další data, můžete spouštět dotazy obou ke shromáždění dat a k nalezení jednotlivých instancí.  [Analýzy](../log-query/log-query-overview.md) představují výkonný nástroj jak pro vysvětlení výkonu, tak i využití a k diagnostickým účelům.

![Příklad analýz](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>Nainstalujte aplikaci na server
Teď publikujte aplikaci na server, dovolte osobám ji používat a sledujte telemetrii zobrazenou na portálu.

* Ujistěte se, že brána firewall umožňuje vaší aplikace odesílat telemetrii na tyto porty:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Pokud je nutné odchozí provoz směrovat přes bránu firewall, nadefinujte systémové vlastnosti `http.proxyHost` a `http.proxyPort`.

* Na serverech Windows nainstalujte:

  * [Distribuovatelné součásti Microsoft Visual C++](https://www.microsoft.com/download/details.aspx?id=40784)

    (Tato komponenta povoluje čítače výkonu.)

## <a name="azure-app-service-aks-vms-config"></a>Azure App Service, AKS, konfigurace virtuálních počítačů

Nejlepším a nejjednodušším přístupem k monitorování aplikací, které běží na jakémkoli poskytovateli prostředků Azure, je použití Application Insights automatické instrumentace přes [agenta Java 3,0](./java-in-process-agent.md).


## <a name="exceptions-and-request-failures"></a>Výjimky a chyby požadavků
Neošetřené výjimky a selhání požadavků jsou automaticky shromažďovány Application Insights webovým filtrem.

Chcete-li shromažďovat data o jiných výjimkách, můžete [do kódu vložit volání trackException ()][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Volání metody monitorování a externí závislosti
[Nainstalujte agenta Java](java-agent.md) k protokolování určených vnitřních metod a volání provedená prostřednictvím JDBC s daty časování.

A pro automatické pojmenovávání operací.

## <a name="w3c-distributed-tracing"></a>Distribuované trasování W3C

Sada Application Insights Java SDK teď podporuje [distribuované trasování W3C](https://w3c.github.io/trace-context/).

Příchozí konfigurace sady SDK je podrobněji vysvětlena v našem článku o [korelaci](correlation.md).

Odchozí konfigurace sady SDK je definována v souboru [AI-Agent.xml](java-agent.md) .

## <a name="performance-counters"></a>Čítače výkonu
Otevřete **prozkoumat**, **metriky**a zobrazte si rozsah čítačů výkonu.

![Snímek obrazovky podokna metriky se zvolenými soukromými bajty procesu](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Vlastní nastavení kolekce čítačů výkonu
Chcete-li zakázat shromažďování standardní sady čítačů výkonu, přidejte do kořenového uzlu souboru *ApplicationInsights.xml* následující kód:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Shromažďování dalších čítačů výkonu
Můžete zadat další čítače výkonu, které se mají shromažďovat.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Čítače JMX (vystavené ve virtuálním počítači Java)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` – název zobrazený na portálu služby Application Insights
* `objectName` – název objektu JMX
* `attribute` – atribut názvu objektu JMX k načtení
* `type` (volitelné) – typ atributu JMX objektu:
  * Výchozí hodnota: jednoduchý typ, například int nebo long.
  * `composite`: data čítače výkonu jsou ve formátu „Attribute.Data“
  * `tabular`: data čítače výkonu jsou ve formátu řádku tabulky

#### <a name="windows-performance-counters"></a>Čítače výkonu Windows
Každý [čítač výkonu systému Windows](/windows/win32/perfctrs/performance-counters-portal) je členem určité kategorie (stejným způsobem, jakým je pole členem třídy). Kategorie mohou být buď globální, nebo mohou mít číslované nebo pojmenované instance.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – Název zobrazený na portálu služby Application Insights.
* categoryName – kategorie čítače výkonu (objekt výkonu) ke kterému je přiřazen tento čítač výkonu.
* counterName – název čítače výkonu.
* instanceName – název instance kategorie čítače výkonu nebo prázdný řetězec (""), pokud kategorie obsahuje jednu instanci. Pokud je categoryName proces a čítač výkonu, který chcete shromáždit, pochází z aktuálního procesu JVM, na kterém běží vaše aplikace, zadejte `"__SELF__"`.

### <a name="unix-performance-counters"></a>Čítače výkonu Unix
* [Nainstalujte collectd s modulem plug-in Application Insights](java-collectd.md) a získejte celou řadu dat systému a sítě.

## <a name="get-user-and-session-data"></a>Získejte data uživatele a relace
Takže odesíláte telemetrii z webového serveru. Teď pokud chcete získat úplné 360stupňové zobrazení vaší aplikace, můžete přidat další monitorování:

* [Přidání telemetrie na webové stránky][usage] pro monitorování zobrazení stránek a metrik uživatelů.
* [Nastavení webových testů][availability] pro zajištění, že aplikace zůstane funkční a bude reagovat.

## <a name="send-your-own-telemetry"></a>Odeslat vlastní telemetrii
Teď, když jste nainstalovali sadu SDK, můžete použít rozhraní API k odeslání vlastní telemetrie.

* [Sledujte vlastní události a metriky][api] a dozvíte se, jak uživatelé pracují s vaší aplikací.
* [Prohledávejte události a protokoly][diagnostic] pro pomoc s diagnostikou problémů.

## <a name="availability-web-tests"></a>Testy dostupnosti webu
Application Insights může otestovat váš web v pravidelných intervalech a zkontrolovat, zda je funkční a dobře reaguje.

[Přečtěte si další informace o tom, jak nastavit webové testy dostupnosti.][availability]

## <a name="questions-problems"></a>Máte otázky? Problémy?
[Řešení potíží Java](java-troubleshoot.md)

## <a name="next-steps"></a>Další kroky
* [Monitorování volání závislostí](java-agent.md)
* [Monitorování čítačů výkonu Unix](java-collectd.md)
* Přidejte [na svoje webové stránky monitorování](javascript.md) a sledujte dobu načítání stránek, volání AJAX nebo výjimky prohlížeče.
* Můžete napsat i [vlastní telemetrii](./api-custom-events-metrics.md) ke sledování využití v prohlížeči nebo na serveru.
* Použití  [analýzy](../log-query/log-query-overview.md) pro výkonné dotazy přes telemetrii z vaší aplikace
* Další informace najdete na webu [Azure pro vývojáře v Javě](/java/azure).

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#trackexception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../platform/metrics-charts.md
[usage]: javascript.md