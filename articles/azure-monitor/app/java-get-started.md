---
title: 'Úvodní příručka: Analýza webových aplikací v Jazyce pomocí Azure Application Insights'
description: 'Sledování výkonu webových aplikací Java pomocí Application Insights '
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 05/24/2019
ms.openlocfilehash: 2355cb7ab995cab3060c7a94c9e7ea344bd9e92b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984636"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Úvodní příručka: Začínáme s Application Insights ve webovém projektu v Jazyce Java

V tomto rychlém startu pomocí Application Insights můžete automaticky instrumentovat požadavek, sledovat závislosti a shromažďovat čítače výkonu, diagnostikovat problémy s výkonem a výjimky a psát kód ke sledování toho, co uživatelé dělají s vaší aplikací.

Application Insights představují rozšiřitelnou analytickou službu, která webovým vývojářům pomůže pochopit výkon a využití živých aplikací. Application Insights podporuje aplikace v Javě spuštěné v systému Linux, Unix nebo Windows.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Funkční java aplikace.

## <a name="get-an-application-insights-instrumentation-key"></a>Získejte klíč instrumentace Application Insights

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Na webu Azure Portalvytvořte prostředek Application Insights. Nastavte typ aplikace na webovou aplikaci Java.

3. Najděte klíč instrumentace nového prostředku. Tento klíč budete muset za chvíli vložit do projektu kódu.

    ![V přehledu nového prostředku klikněte na tlačítko Vlastnosti a zkopírujte klíč instrumentace](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>Do projektu přidejte Application Insights SDK pro jazyk Java

*Zvolte typ projektu.*

# <a name="maven"></a>[Maven](#tab/maven)

Pokud je projekt již nastaven na použití Maven pro sestavení, sloučit následující kód do souboru *pom.xml.*

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

Pokud je projekt již nastaven na použití Gradle pro sestavení, sloučit následující kód do souboru *build.gradle.*

Pak obnovte závislosti projektu k získání stažených binárních souborů.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

# <a name="other-types"></a>[Jiné typy](#tab/other)

Stáhněte si [nejnovější verzi](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) a zkopírujte do svého projektu potřebné soubory, přičemž nahraďte jejich starší verze.

---

### <a name="questions"></a>Dotazy
* *Jaký je vztah mezi `-web-auto` `-web` , `-core` a komponenty?*
  * `applicationinsights-web-auto`poskytuje metriky, které sledují počty požadavků na servlet HTTP a doby odezvy, a to automatickou registrací filtru servlet Application Insights za běhu.
  * `applicationinsights-web`Také poskytuje metriky, které sledují počet požadavků na servlet HTTP a doby odezvy, ale vyžaduje ruční registraci filtru servlet Application Insights ve vaší aplikaci.
  * `applicationinsights-core`poskytuje pouze holé rozhraní API, například pokud vaše aplikace není založená servlet.
  
* *Jak mám aktualizovat sadu SDK na nejnovější verzi?*
  * Pokud používáte Gradle nebo Maven...
    * Aktualizujte soubor sestavení a zadejte nejnovější verzi.
  * Pokud ručně spravujete závislosti...
    * Stáhněte si poslední [Application Insights SDK pro jazyk Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) a nahraďte staré. Změny jsou popsány v [poznámkách k verzi sady SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="add-an-applicationinsightsxml-file"></a>Přidání souboru *ApplicationInsights.xml*
Přidejte *soubor ApplicationInsights.xml* do složky prostředků v projektu nebo se ujistěte, že je přidán do cesty třídy nasazení projektu. Zkopírujte do něj následující kód XML.

Nahraďte klíč instrumentace klíčem, který jste získali z portálu Azure.

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

Volitelně může být konfigurační soubor v libovolném umístění přístupném vaší aplikaci.  Vlastnost `-Dapplicationinsights.configurationDirectory` system určuje adresář, který obsahuje *soubor ApplicationInsights.xml*. Například konfigurační soubor umístěný v cestě `E:\myconfigs\appinsights\ApplicationInsights.xml` se nakonfiguruje pomocí vlastnosti `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* Klíč instrumentace se zasílá společně s každou položkou telemetrie a říká službě Application Insights, aby ho zobrazila v prostředku.
* Požadavek komponenty HTTP je volitelný. Automaticky odesílá telemetrii týkající se žádostí a časů odezvy na portál.
* Korelace událostí je doplněk komponenty požadavku HTTP. Přiřadí identifikátor každému požadavku přijatému serverem. Potom přidá tento identifikátor jako vlastnost ke každé položce telemetrie jako vlastnost "Operation.Id". Umožňuje korelovat telemetrii související s každou žádostí nastavením filtru v [diagnostickém vyhledávání][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Alternativní způsoby nastavení klíče instrumentace
Application Insights SDK hledá klíče v tomto pořadí:

1. Vlastnost systému: -DAPPINSIGHTS_INSTRUMENTATIONKEY=your_ikey
2. Proměnná prostředí: APPINSIGHTS_INSTRUMENTATIONKEY
3. Konfigurační soubor: *ApplicationInsights.xml*

Můžete ho taky [nastavit v kódu](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Přidat agenta

[Nainstalujte agenta Jazyka Java](java-agent.md) pro zachycení odchozích volání HTTP, dotazů JDBC, protokolování aplikací a lepšího pojmenování operací.

## <a name="run-your-application"></a>Spusťte aplikaci
Buď ji spusťte v režimu ladění na vývojovém počítači, nebo publikujte na serveru.

## <a name="view-your-telemetry-in-application-insights"></a>Zobrazte telemetrii ve službě Application Insights
Vraťte se do prostředku Application Insights na web [Microsoft Azure Portal](https://portal.azure.com).

Data požadavků HTTP se zobrazí v okně přehledu. (Pokud zde nejsou, počkejte několik sekund a pak klikněte na tlačítko Aktualizovat.)

![Snímek obrazovky s ukázkovými daty přehledu](./media/java-get-started/overview-graphs.png)

[Další informace o metrikách.][metrics]

Proklikejte se prostřednictvím jakékoli grafu pro zobrazení podrobnějších agregovaných metrik.

![Podokno selhání přehledů aplikací s grafy](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Data instance
Proklikejte se jednotlivými typy konkrétního požadavku pro zobrazení jednotlivých instancí.

![Přechod k podrobnostem do konkrétního zobrazení vzorku](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analýzy: účinný dotazovací jazyk
Jak shromažďujete další data, můžete spouštět dotazy obou ke shromáždění dat a k nalezení jednotlivých instancí.  [Analýzy](../../azure-monitor/app/analytics.md) představují výkonný nástroj jak pro vysvětlení výkonu, tak i využití a k diagnostickým účelům.

![Příklad analýz](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>Nainstalujte aplikaci na server
Teď publikujte aplikaci na server, dovolte osobám ji používat a sledujte telemetrii zobrazenou na portálu.

* Ujistěte se, že brána firewall umožňuje vaší aplikace odesílat telemetrii na tyto porty:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Pokud je nutné odchozí provoz směrovat přes bránu firewall, nadefinujte systémové vlastnosti `http.proxyHost` a `http.proxyPort`.

* Na serverech Windows nainstalujte:

  * [Microsoft Visual C++ Redistributable](https://www.microsoft.com/download/details.aspx?id=40784)

    (Tato komponenta povoluje čítače výkonu.)

## <a name="azure-app-service-config-spring-boot"></a>Konfigurace služby Azure App Service (jarní spuštění)

Aplikace spring boot u windows vyžadují další konfiguraci ke spuštění ve službě Azure App Services. Upravte **web.config** a přidejte následující konfiguraci:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Výjimky a chyby požadavků
Neošetřené výjimky a selhání požadavků jsou automaticky shromažďovány webovým filtrem Application Insights.

Chcete-li shromažďovat data o jiných výjimkách, můžete [vložit volání trackException() ve vašem kódu][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Volání metody monitorování a externí závislosti
[Nainstalujte agenta Java](java-agent.md) k protokolování určených vnitřních metod a volání provedená prostřednictvím JDBC s daty časování.

A pro automatické pojmenování operací.

## <a name="w3c-distributed-tracing"></a>Distribuované trasování W3C

Sada Application Insights Java SDK nyní podporuje [distribuované trasování W3C](https://w3c.github.io/trace-context/).

Příchozí konfigurace sady SDK je vysvětlena dále v našem článku o [korelaci](correlation.md).

Odchozí konfigurace sady SDK je definována v souboru [AI-Agent.xml.](java-agent.md)

## <a name="performance-counters"></a>Čítače výkonu
Otevřete **prozkoumat**, **Metriky**, chcete-li zobrazit rozsah čítačů výkonu.

![Snímek obrazovky podokna metrik s vybranými soukromými bajty procesu](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Vlastní nastavení kolekce čítačů výkonu
Chcete-li zakázat shromažďování standardní sady čítačů výkonu, přidejte pod kořenový uzel souboru *ApplicationInsights.xml* následující kód:

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
* `type`(nepovinné) - Typ atributu objektu JMX:
  * Výchozí hodnota: jednoduchý typ, například int nebo long.
  * `composite`: data čítače výkonu jsou ve formátu „Attribute.Data“
  * `tabular`: data čítače výkonu jsou ve formátu řádku tabulky

#### <a name="windows-performance-counters"></a>Čítače výkonu Windows
Každý [čítač výkonu systému Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) je členem určité kategorie (stejným způsobem, jakým je pole členem třídy). Kategorie mohou být buď globální, nebo mohou mít číslované nebo pojmenované instance.

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

[Přečtěte si další informace o nastavení webových testů dostupnosti.][availability]

## <a name="questions-problems"></a>Máte dotazy? Problémy?
[Řešení potíží Java](java-troubleshoot.md)

## <a name="next-steps"></a>Další kroky
* [Monitorování volání závislostí](java-agent.md)
* [Monitorování čítačů výkonu Unix](java-collectd.md)
* Přidejte [na svoje webové stránky monitorování](javascript.md) a sledujte dobu načítání stránek, volání AJAX nebo výjimky prohlížeče.
* Můžete napsat i [vlastní telemetrii](../../azure-monitor/app/api-custom-events-metrics.md) ke sledování využití v prohlížeči nebo na serveru.
* Použití [Služby Analytics](../../azure-monitor/app/analytics.md) pro výkonné dotazy na telemetrii z vaší aplikace
* Další informace najdete na webu [Azure pro vývojáře v Javě](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
