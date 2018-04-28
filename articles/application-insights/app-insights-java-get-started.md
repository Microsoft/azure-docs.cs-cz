---
title: Analýzy webové aplikace Java pomocí Azure Application Insights | Dokumentace Microsoftu
description: 'Sledování výkonu webových aplikací Java pomocí Application Insights '
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: e1c4ea08b71c2e99b6c8e839e1fddc3172ca251d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Začínáme s Application Insights ve webovém projektu Java


[Application Insights](https://azure.microsoft.com/services/application-insights/) představují rozšiřitelnou analytickou službu, která webovým vývojářům pomůže pochopit výkon a využití živých aplikací. Použijte ji k [zjištění a diagnostice výjimek a problémů s výkonem](app-insights-detect-triage-diagnose.md) a [napište kód][api] pro sledování, co uživatelé s vaší aplikací dělají.

![ukázková data](./media/app-insights-java-get-started/5-results.png)

Application Insights podporuje aplikace v Javě spuštěné v systému Linux, Unix nebo Windows.

Budete potřebovat:

* JRE verze 1.7 nebo 1.8
* Předplatné [Microsoft Azure](https://azure.microsoft.com/).

*Pokud máte webovou aplikaci, která je už v provozu, můžete použít alternativní postup [přidání sady SDK za běhu na webovém serveru](app-insights-java-live.md). S touto alternativou se vyhnete opětovnému sestavování kódu, ale nebudete mít možnost napsat kód ke sledování činnosti uživatelů.*

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Získejte klíč instrumentace Application Insights
1. Přihlaste se na web [Microsoft Azure Portal](https://portal.azure.com).
2. Vytvořte prostředek Application Insights. Nastavte typ aplikace na webovou aplikaci Java.

    ![Zadejte název, vyberte webovou aplikaci Java a klikněte na možnost Vytvořit](./media/app-insights-java-get-started/02-create.png)
3. Najděte klíč instrumentace nového prostředku. Tento klíč budete muset za chvíli vložit do projektu kódu.

    ![V přehledu nového prostředku klikněte na tlačítko Vlastnosti a zkopírujte klíč instrumentace](./media/app-insights-java-get-started/03-key.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Do projektu přidejte Application Insights SDK pro jazyk Java
*Zvolte vhodný způsob pro váš projekt.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Pokud používáte Maven... <a name="maven-setup" />
Pokud je váš projekt již nastaven na sestavení s použitím nástroje Maven, slučte následující kód do souboru pom.xml.

Pak obnovte závislosti projektu k získání stažených binárních souborů.

```XML

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[2.0,)</version>
      </dependency>
    </dependencies>
```

* *Chyby ověření sestavení nebo kontrolního součtu?* Zkuste použít konkrétní verzi, například: `<version>2.0.n</version>`. Nejnovější verzi najdete v [poznámkách k verzi sady SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) nebo v [artefaktech Maven](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Je nutné přejít na novou sadu SDK?* Obnovte závislosti svého projektu.

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Pokud používáte Gradle... <a name="gradle-setup" />
Pokud je váš projekt již nastaven na sestavení s použitím nástroje Gradle, slučte následující kód do souboru build.gradle.

Pak obnovte závislosti projektu k získání stažených binárních souborů.

```gradle

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '2.+'
      // or applicationinsights-core for bare API
    }
```

#### <a name="if-youre-using-eclipse-to-create-a-dynamic-web-project-"></a>Pokud k vytvoření dynamického webového projektu používáte Eclipse...
Použijte [Modul Application Insights SDK pro jazyk Java][eclipse]. Poznámka: I když se použitím tohoto modulu plug-in zrychlí zprovoznění Application Insights (za předpokladu, že nepoužíváte Maven nebo Gradle), nejedná se o systém správy závislostí. Při aktualizaci modulu plug-in proto nedojde k automatické aktualizaci knihoven Application Insights ve vašem projektu.

* *Chyby ověření sestavení nebo kontrolního součtu?* Zkuste použít konkrétní verzi, například: `version:'2.0.n'`. Nejnovější verzi najdete v [poznámkách k verzi sady SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) nebo v [artefaktech Maven](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Aktualizace na novou sadu SDK* Aktualizujte závislosti svého projektu.

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>Jinak, pokud spravujete závislosti ručně...
Stáhněte si [nejnovější verzi](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) a zkopírujte do svého projektu potřebné soubory, přičemž nahraďte jejich starší verze.

### <a name="questions"></a>Otázky...
* *Jaký je vztah mezi komponentami `-core` a `-web`?*
  * `applicationinsights-core` poskytuje úplné informace o API. Tuto komponentu budete vždy potřebovat.
  * `applicationinsights-web` poskytuje metriky, které sledují počty žádostí HTTP a časy odezvy. Tuto komponentu můžete vynechat, pokud nechcete automaticky shromažďovat tuto telemetrii. Hodí se to například v případě, že chcete napsat vlastní.
  
* *Jak mám aktualizovat sadu SDK na nejnovější verzi?*
  * Pokud používáte Gradle nebo Maven...
    * Aktualizujte svůj soubor sestavení zadáním nejnovější verze nebo pomocí syntaxe zástupných znaků Gradle nebo Maven zahrňte nejnovější verzi automaticky. Pak aktualizujte závislosti svého projektu. Syntaxi zástupných znaků si můžete prohlédnout ve výše uvedených příkladech pro [Gradle](#gradle-setup) nebo [Maven](#maven-setup).
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
* Korelace událostí je doplněk komponenty požadavku HTTP. Přiřadí identifikátor každé žádosti přijaté serverem a přidá ho jako vlastnost každé položce telemetrie jako vlastnost Operation.Id. Umožňuje korelovat telemetrii související s každou žádostí nastavením filtru v [diagnostickém vyhledávání][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Alternativní způsoby nastavení klíče instrumentace
Application Insights SDK hledá klíče v tomto pořadí:

1. Systémová vlastnost: -DAPPLICATION_INSIGHTS_IKEY=váš_ikey
2. Proměnná prostředí: APPLICATION_INSIGHTS_IKEY
3. Konfigurační soubor: ApplicationInsights.xml

Můžete ho taky [nastavit v kódu](app-insights-api-custom-events-metrics.md#ikey):

```Java
    TelemetryConfiguration.getActive().setInstrumentationKey(iKey);
```

## <a name="4-add-an-http-filter"></a>4. Přidat filtr HTTP
Poslední krok konfigurace umožňuje komponentě požadavku HTTP zaprotokolovat každý webový požadavek. (Není požadováno, pokud chcete úplné rozhraní API.)

### <a name="spring-boot-applications"></a>Aplikace Spring Boot
Zaregistrujte ve své třídě Configuration filtr Application Insights `WebRequestTrackingFilter`:

```Java
package devCamp.WebApp.configurations;

    import javax.servlet.Filter;

    import org.springframework.boot.context.embedded.FilterRegistrationBean;
    import org.springframework.context.annotation.Bean;
    import org.springframework.core.Ordered;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.context.annotation.Configuration;
    import com.microsoft.applicationinsights.TelemetryConfiguration;
    import com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter;


    @Configuration
    public class AppInsightsConfig {

    //Initialize AI TelemetryConfiguration via Spring Beans
        @Bean
        public String telemetryConfig() {
            String telemetryKey = System.getenv("APPLICATION_INSIGHTS_IKEY");
            if (telemetryKey != null) {
                TelemetryConfiguration.getActive().setInstrumentationKey(telemetryKey);
            }
            return telemetryKey;
        }
    
    //Set AI Web Request Tracking Filter
        @Bean
        public FilterRegistrationBean aiFilterRegistration(@Value("${spring.application.name:application}") String applicationName) {
           FilterRegistrationBean registration = new FilterRegistrationBean();
           registration.setFilter(new WebRequestTrackingFilter(applicationName));
           registration.setName("webRequestTrackingFilter");
           registration.addUrlPatterns("/*");
           registration.setOrder(Ordered.HIGHEST_PRECEDENCE + 10);
           return registration;
       } 

    //Set up AI Web Request Tracking Filter
        @Bean(name = "WebRequestTrackingFilter")
        public Filter webRequestTrackingFilter(@Value("${spring.application.name:application}") String applicationName) {
            return new WebRequestTrackingFilter(applicationName);
        }   
    }
```

Tato třída nakonfiguruje `WebRequestTrackingFilter` jako první filtr v řetězu filtrů HTTP. Kromě toho z proměnné prostředí operačního systému přetáhne klíč instrumentace, pokud je k dispozici.

> Vzhledem k tomu, že se jedná o aplikaci Spring Boot s vlastní konfigurací Spring MVC, místo konfigurace Spring MVC používáme konfiguraci webového filtru HTTP. Konfiguraci specifickou pro Spring MVC najdete v následujících částech.


### <a name="applications-using-webxml"></a>Aplikace využívající soubor Web.xml
Vyhledejte a otevřete soubor web.xml ve vašem projektu a slučte následující kód pod uzlem webové aplikace, které jsou nakonfigurované filtry aplikace.

Chcete-li získat nejpřesnější výsledky, musí být filtr namapován před všemi ostatními filtry.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>
```

#### <a name="if-youre-using-spring-web-mvc-31-or-later"></a>Pokud používáte Spring Web MVC 3.1 nebo novější
Upravte tyto prvky v souboru *-servlet.xml, aby zahrnovaly balíček Application Insights:

```XML

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>
```

#### <a name="if-youre-using-struts-2"></a>Pokud používáte Struts 2
Tuto položku přidáte do konfiguračního souboru Struts (obvykle s názvem struts.xml nebo struts default.xml):

```XML

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />
```

Pokud máte sběrače definované ve výchozím zásobníku, lze sběrač přidat do tohoto balíku.

## <a name="5-run-your-application"></a>5. Spusťte aplikaci
Buď ji spusťte v režimu ladění na vývojovém počítači, nebo publikujte na serveru.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Zobrazte telemetrii ve službě Application Insights
Vraťte se do prostředku Application Insights na web [Microsoft Azure Portal](https://portal.azure.com).

Data požadavků HTTP se zobrazí v okně přehledu. (Pokud zde nejsou, počkejte několik sekund a pak klikněte na tlačítko Aktualizovat.)

![ukázková data](./media/app-insights-java-get-started/5-results.png)

[Další informace o metrikách.][metrics]

Proklikejte se prostřednictvím jakékoli grafu pro zobrazení podrobnějších agregovaných metrik.

![](./media/app-insights-java-get-started/6-barchart.png)

> Application Insights předpokládá, že formát požadavků HTTP pro aplikace MVC je: `VERB controller/action`. Například `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` a `GET Home/Product/sdf96vws` se seskupí do `GET Home/Product`. Toto seskupení umožňuje smysluplné agregace požadavků, jako je počet požadavků a průměrná doba provádění pro požadavky.
>
>

### <a name="instance-data"></a>Data instance
Proklikejte se jednotlivými typy konkrétního požadavku pro zobrazení jednotlivých instancí.

Ve službě Application Insights se zobrazí dva druhy dat: agregovaná data, uložená a zobrazená jako průměry, počty a součty; a data instancí – jednotlivé sestavy požadavků protokolu HTTP, výjimky, zobrazení stránek nebo uživatelské události.

Při zobrazení vlastností požadavku uvidíte telemetrické události související s například požadavky a výjimkami.

![](./media/app-insights-java-get-started/7-instance.png)

### <a name="analytics-powerful-query-language"></a>Analýzy: účinný dotazovací jazyk
Jak shromažďujete další data, můžete spouštět dotazy obou ke shromáždění dat a k nalezení jednotlivých instancí.  [Analýzy](app-insights-analytics.md) představují výkonný nástroj jak pro vysvětlení výkonu, tak i využití a k diagnostickým účelům.

![Příklad analýz](./media/app-insights-java-get-started/025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Nainstalujte aplikaci na server
Teď publikujte aplikaci na server, dovolte osobám ji používat a sledujte telemetrii zobrazenou na portálu.

* Ujistěte se, že brána firewall umožňuje vaší aplikace odesílat telemetrii na tyto porty:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Pokud je nutné odchozí provoz směrovat přes bránu firewall, nadefinujte systémové vlastnosti `http.proxyHost` a `http.proxyPort`.

* Na serverech Windows nainstalujte:

  * [Microsoft Visual C++ Redistributable](http://www.microsoft.com/download/details.aspx?id=40784)

    (Tato komponenta povoluje čítače výkonu.)


## <a name="exceptions-and-request-failures"></a>Výjimky a chyby požadavků
Nezpracované výjimky jsou shromažďovány automaticky:

![Otevřete Nastavení, Selhání.](./media/app-insights-java-get-started/21-exceptions.png)

Chcete-li shromažďovat data o dalších výjimkách, máte dvě možnosti:

* [Vložit do kódu volání metody trackException()][apiexceptions].
* [Nainstalovat na server agenta Java](app-insights-java-agent.md). Určete metody, které chcete sledovat.

## <a name="monitor-method-calls-and-external-dependencies"></a>Volání metody monitorování a externí závislosti
[Nainstalujte agenta Java](app-insights-java-agent.md) k protokolování určených vnitřních metod a volání provedená prostřednictvím JDBC s daty časování.

## <a name="performance-counters"></a>Čítače výkonu
Klikněte na **Nastavení**, **Servery** a uvidíte rozsah čítačů výkonu.

![](./media/app-insights-java-get-started/11-perf-counters.png)

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

Čítače výkonu jsou zobrazené jako vlastní metriky v [Průzkumníku metrik][metrics].

![](./media/app-insights-java-get-started/12-custom-perfs.png)

### <a name="unix-performance-counters"></a>Čítače výkonu Unix
* [Nainstalujte collectd s modulem plug-in Application Insights](app-insights-java-collectd.md) a získejte celou řadu dat systému a sítě.

## <a name="get-user-and-session-data"></a>Získejte data uživatele a relace
Takže odesíláte telemetrii z webového serveru. Teď pokud chcete získat úplné 360stupňové zobrazení vaší aplikace, můžete přidat další monitorování:

* [Přidání telemetrie na webové stránky][usage] pro monitorování zobrazení stránek a metrik uživatelů.
* [Nastavení webových testů][availability] pro zajištění, že aplikace zůstane funkční a bude reagovat.

## <a name="capture-log-traces"></a>Zaznamenat trasování protokolu
Službu Application Insights můžete použít k nařezání a rozčlenění protokolů z Log4J, Logback nebo jiných rozhraní protokolování. Protokoly mohou korelovat s požadavky HTTP a další telemetrií. [Zjistěte jak][javalogs].

## <a name="send-your-own-telemetry"></a>Odeslat vlastní telemetrii
Teď, když jste nainstalovali sadu SDK, můžete použít rozhraní API k odeslání vlastní telemetrie.

* [Sledujte vlastní události a metriky][api] a dozvíte se, jak uživatelé pracují s vaší aplikací.
* [Prohledávejte události a protokoly][diagnostic] pro pomoc s diagnostikou problémů.

## <a name="availability-web-tests"></a>Testy dostupnosti webu
Application Insights může otestovat váš web v pravidelných intervalech a zkontrolovat, zda je funkční a dobře reaguje. [Nastavíte je][availability] kliknutím na Webové testy.

![Klikněte na Webové testy a pak přidejte webový test.](./media/app-insights-java-get-started/31-config-web-test.png)

Získáte tabulky s dobami odezvy a navíc e-mailová oznámení, pokud váš web nefunguje.

![Příklad webového testu](./media/app-insights-java-get-started/appinsights-10webtestresult.png)

[Další informace o testech dostupnosti webu.][availability]

## <a name="questions-problems"></a>Máte dotazy? Problémy?
[Řešení potíží s Javou](app-insights-java-troubleshoot.md)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Další kroky
* [Monitorování volání závislostí](app-insights-java-agent.md)
* [Monitorování čítačů výkonu Unix](app-insights-java-collectd.md)
* Přidejte [na svoje webové stránky monitorování](app-insights-javascript.md) a sledujte dobu načítání stránek, volání AJAX nebo výjimky prohlížeče.
* Můžete napsat i [vlastní telemetrii](app-insights-api-custom-events-metrics.md) ke sledování využití v prohlížeči nebo na serveru.
* Vytvářejte si [řídicí panely](app-insights-dashboards.md), kde budete mít pohromadě klíčové grafy pro monitorování systému.
* [Analytické funkce](app-insights-analytics.md) vám pomůžou přímo z vaší aplikace zadávat efektivní dotazy na telemetrie.
* Další informace najdete na webu [Azure pro vývojáře v Javě](/java/azure).

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#trackexception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-javascript.md
