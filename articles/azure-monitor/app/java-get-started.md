---
title: Analýzy webové aplikace Java pomocí Azure Application Insights | Dokumentace Microsoftu
description: 'Sledování výkonu webových aplikací Java pomocí Application Insights '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: lagayhar
ms.openlocfilehash: a6e8187a085d637ad3abc650daf15d92b96755a3
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338118"
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Začínáme s Application Insights ve webovém projektu Java

[Application Insights](https://azure.microsoft.com/services/application-insights/) představují rozšiřitelnou analytickou službu, která webovým vývojářům pomůže pochopit výkon a využití živých aplikací. Slouží k [automatickému instrumentování požadavků, sledování závislostí a shromažďování čítačů výkonu](auto-collect-dependencies.md#java), diagnostice problémů s výkonem a výjimek a [psaní kódu][api] ke sledování toho, co uživatelé s vaší aplikací dělají. 

![Snímek obrazovky s přehledem ukázkových dat](./media/java-get-started/overview-graphs.png)

Application Insights podporuje aplikace v Javě spuštěné v systému Linux, Unix nebo Windows.

Budete potřebovat:

* Java 7 nebo novější
* Předplatné [Microsoft Azure](https://azure.microsoft.com/).

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Získejte klíč instrumentace Application Insights
1. Přihlaste se na web [Microsoft Azure Portal](https://portal.azure.com).
2. Vytvořte prostředek Application Insights. Nastavte typ aplikace na webovou aplikaci Java.

3. Najděte klíč instrumentace nového prostředku. Tento klíč budete muset za chvíli vložit do projektu kódu.

    ![V přehledu nového prostředku klikněte na tlačítko Vlastnosti a zkopírujte klíč instrumentace](./media/java-get-started/instrumentation-key-001.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Do projektu přidejte Application Insights SDK pro jazyk Java
*Zvolte vhodný způsob pro váš projekt.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Pokud používáte Maven... <a name="maven-setup" />
Pokud je váš projekt již nastaven na sestavení s použitím nástroje Maven, slučte následující kód do souboru pom.xml.

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

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Pokud používáte Gradle... <a name="gradle-setup" />
Pokud je váš projekt již nastaven na sestavení s použitím nástroje Gradle, slučte následující kód do souboru build.gradle.

Pak obnovte závislosti projektu k získání stažených binárních souborů.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>Jinak, pokud spravujete závislosti ručně...
Stáhněte si [nejnovější verzi](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) a zkopírujte do svého projektu potřebné soubory, přičemž nahraďte jejich starší verze.

### <a name="questions"></a>Otázky...
* *Jaký je vztah mezi `-web-auto` `-web` komponentami a a `-core` ?*
  * `applicationinsights-web-auto`poskytuje metriky, které sledují počty požadavků HTTP servlet a doby odezvy, automatickým registrací filtru Application Insights servlet za běhu.
  * `applicationinsights-web`také poskytuje metriky, které sledují počty požadavků HTTP servlet a doby odezvy, ale vyžaduje ruční registraci filtru Application Insights servlet ve vaší aplikaci.
  * `applicationinsights-core`poskytuje pouze úplné rozhraní API, například pokud vaše aplikace není založená na servlet.
  
* *Jak mám aktualizovat sadu SDK na nejnovější verzi?*
  * Pokud používáte Gradle nebo Maven...
    * Aktualizujte soubor sestavení tak, aby určoval nejnovější verzi.
  * Pokud spravujete závislosti ručně...
    * Stáhněte si poslední [Application Insights SDK pro jazyk Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) a nahraďte staré. Změny jsou popsány v [poznámkách k verzi sady SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="3-add-an-applicationinsightsxml-file"></a>3. Přidejte soubor ApplicationInsights.xml
Přidejte soubor ApplicationInsights.xml do složky zdrojů v projektu nebo zajistěte, aby byl přidán do cesty nasazení tříd projektu. Zkopírujte do něj následující kód XML.

Nahraďte klíč instrumentace, který jste dostali z portálu Azure.

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

Volitelně se konfigurační soubor může nacházet v jakémkoli umístění, ke kterému má vaše aplikace přístup.  Systémová vlastnost `-Dapplicationinsights.configurationDirectory` určuje adresář obsahující soubor ApplicationInsights.xml. Například konfigurační soubor umístěný v cestě `E:\myconfigs\appinsights\ApplicationInsights.xml` se nakonfiguruje pomocí vlastnosti `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* Klíč instrumentace se zasílá společně s každou položkou telemetrie a říká službě Application Insights, aby ho zobrazila v prostředku.
* Požadavek komponenty HTTP je volitelný. Automaticky odesílá telemetrii týkající se žádostí a časů odezvy na portál.
* Korelace událostí je doplněk komponenty požadavku HTTP. Přiřadí identifikátor každé žádosti přijaté serverem a přidá ho jako vlastnost každé položce telemetrie jako vlastnost Operation.Id. Umožňuje korelaci telemetrie spojené s jednotlivými požadavky nastavením filtru v [diagnostickém vyhledávání][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Alternativní způsoby nastavení klíče instrumentace
Application Insights SDK hledá klíče v tomto pořadí:

1. Systémová vlastnost:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Proměnná prostředí: APPINSIGHTS_INSTRUMENTATIONKEY
3. Konfigurační soubor: ApplicationInsights.xml

Můžete ho taky [nastavit v kódu](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="4-add-agent"></a>4. Přidat agenta

[Nainstalujte agenta Java](java-agent.md) , abyste mohli zachytit odchozí volání http, dotazy JDBC, protokolování aplikací a lepší pojmenovávání operací.

## <a name="5-run-your-application"></a>5. Spusťte aplikaci
Buď ji spusťte v režimu ladění na vývojovém počítači, nebo publikujte na serveru.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Zobrazte telemetrii ve službě Application Insights
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

### <a name="analytics-powerful-query-language"></a>Analytics Výkonný dotazovací jazyk
Jak shromažďujete další data, můžete spouštět dotazy obou ke shromáždění dat a k nalezení jednotlivých instancí.  [Analýzy](../../azure-monitor/app/analytics.md) představují výkonný nástroj jak pro vysvětlení výkonu, tak i využití a k diagnostickým účelům.

![Příklad analýz](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Nainstalujte aplikaci na server
Teď publikujte aplikaci na server, dovolte osobám ji používat a sledujte telemetrii zobrazenou na portálu.

* Ujistěte se, že brána firewall umožňuje vaší aplikace odesílat telemetrii na tyto porty:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Pokud je nutné odchozí provoz směrovat přes bránu firewall, nadefinujte systémové vlastnosti `http.proxyHost` a `http.proxyPort`.

* Na serverech Windows nainstalujte:

  * [Microsoft Visual C++ Redistributable](https://www.microsoft.com/download/details.aspx?id=40784)

    (Tato komponenta povoluje čítače výkonu.)

## <a name="azure-app-service-config-spring-boot"></a>Azure App Service config (jarní spuštění)

Aplikace pro spouštění pružiny spuštěné v systému Windows vyžadují další konfiguraci pro spuštění v Azure App Services. Upravte soubor **Web. config** a přidejte následující:

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
Neošetřené výjimky a selhání požadavků jsou automaticky shromažďovány Application Insights webovým filtrem.

Chcete-li shromažďovat data o jiných výjimkách, můžete [do kódu vložit volání trackException ()][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Volání metody monitorování a externí závislosti
[Nainstalujte agenta Java](java-agent.md) k protokolování určených vnitřních metod a volání provedená prostřednictvím JDBC s daty časování.

A pro automatické pojmenovávání operací.

## <a name="w3c-distributed-tracing"></a>Distribuované trasování W3C

Sada Application Insights Java SDK teď podporuje [distribuované trasování W3C](https://w3c.github.io/trace-context/).

Příchozí konfigurace sady SDK je podrobněji vysvětlena v našem článku [](correlation.md#telemetry-correlation-in-the-java-sdk)o korelaci.

Odchozí konfigurace sady SDK je definována v souboru [AI-agent. XML](java-agent.md) .

## <a name="performance-counters"></a>Čítače výkonu
Otevřete **prozkoumat**, **metriky**a zobrazte si rozsah čítačů výkonu.

![Snímek obrazovky podokna metriky se zvolenými soukromými bajty procesu](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Vlastní nastavení kolekce čítačů výkonu
Pro zakázání shromažďování standardní sady čítačů výkonu přidejte následující kód do kořenového uzlu souboru ApplicationInsights.xml:

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
* `type`(volitelné) – typ atributu JMX objektu:
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

* [Přidejte telemetrii k webovým stránkám][usage] a sledujte zobrazení stránek a metriky uživatelů.
* [Nastavte webové testy][availability] , abyste měli jistotu, že vaše aplikace zůstane v provozu a reaguje.

## <a name="send-your-own-telemetry"></a>Odeslat vlastní telemetrii
Teď, když jste nainstalovali sadu SDK, můžete použít rozhraní API k odeslání vlastní telemetrie.

* [Sledujte vlastní události a metriky][api] , abyste se dozvěděli, co uživatelé s vaší aplikací dělají.
* [Prohledejte události a protokoly][diagnostic] , které vám pomůžou diagnostikovat problémy.

## <a name="availability-web-tests"></a>Testy dostupnosti webu
Application Insights může otestovat váš web v pravidelných intervalech a zkontrolovat, zda je funkční a dobře reaguje.

[Přečtěte si další informace o tom, jak nastavit webové testy dostupnosti.][availability]

## <a name="questions-problems"></a>Máte dotazy? Problémy?
[Řešení potíží s Javou](java-troubleshoot.md)

## <a name="next-steps"></a>Další kroky
* [Monitorování volání závislostí](java-agent.md)
* [Monitorování čítačů výkonu Unix](java-collectd.md)
* Přidejte [na svoje webové stránky monitorování](javascript.md) a sledujte dobu načítání stránek, volání AJAX nebo výjimky prohlížeče.
* Můžete napsat i [vlastní telemetrii](../../azure-monitor/app/api-custom-events-metrics.md) ke sledování využití v prohlížeči nebo na serveru.
* [Analytické funkce](../../azure-monitor/app/analytics.md) vám pomůžou přímo z vaší aplikace zadávat efektivní dotazy na telemetrie.
* Další informace najdete na webu [Azure pro vývojáře v Javě](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
