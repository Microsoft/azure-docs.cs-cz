---
title: Monitorování výkonu webových aplikací v Jazyce – Azure Application Insights
description: Rozšířené sledování výkonu a využití vašich webových stránek java s Application Insights.
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: b29618179d22eac97a07bf41906465aba1fd7929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657023"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Sledování závislostí, zachycených výjimek a časů spuštění metod ve webových aplikacích Java


Pokud jste [svou webovou aplikaci Java instrumentovali pomocí Application Insights][java], můžete pomocí Java Agenta získat hlubší přehledy bez jakýchkoli změn kódu:

* **Závislosti:** Data o volání, která vaše aplikace provádí do jiných součástí, včetně:
  * **Odchozí HTTP volání** uskutečněná přes Apache HttpClient, OkHttp a `java.net.HttpURLConnection` jsou zachycena.
  * **Redisovy hovory** přes klienta Jediů jsou zachyceny.
  * **JDBC dotazy** - Pro MySQL a PostgreSQL, pokud volání trvá déle než 10 sekund, agent hlásí plán dotazu.

* **Protokolování aplikací:** Zachycování a korelace protokolů aplikací s požadavky HTTP a další telemetrií
  * **Log4j 1,2**
  * **Protokol4j2**
  * **Přihlašování**

* **Lepší pojmenování operací:** (používá se pro agregaci požadavků na portálu)
  * **Jaro** - `@RequestMapping`na základě .
  * **JAX-RS** - `@Path`na základě . 

Chcete-li použít agenta Java, nainstalujte jej na server. Vaše webové aplikace musí být instrumentovány sadou [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instalace agenta Application Insights pro Jazyk Java
1. Na počítači s vaším serverem Java si [stáhněte agenta](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Nezapomeňte si stáhnout stejnou verzi agenta Java, jako je verze základních a webových balíčků sady Application Insights Java SDK.
2. Upravte spouštěcí skript aplikačního serveru a přidejte následující argument JVM:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Například v Tomcatu na počítači s Linuxem:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Restartujte aplikační server.

## <a name="configure-the-agent"></a>Konfigurace agenta
Vytvořte soubor `AI-Agent.xml` s názvem a umístěte jej do stejné složky jako soubor JAR agenta.

Nastavte obsah souboru XML. Upravte následující příklad tak, aby zahrnoval nebo vynechel požadované funkce.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>Další konfigurace (jarní spuštění)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Pro Azure App Services postupujte takto:

* Klikněte na Nastavení > Nastavení aplikace.
* V části Nastavení aplikace přidejte novou dvojici klíče a hodnoty:

Klíč: `JAVA_OPTS` Hodnota:`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

Nejnovější verzi java agenta naleznete v [vydáních zde](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

Agent musí být zabalen jako zdroj v projektu tak, aby skončil v adresáři D:/home/site/wwwroot/. Můžete potvrdit, že váš agent je ve správném adresáři služby App Service tím, že přejdete na **vývojové nástroje** > **Rozšířené nástroje** > **ladicí konzoly** a zkoumání obsahu adresáře webu.    

* Kliknutím na Uložit uložte nastavení a kliknutím na Restartovat restartujte aplikaci. (Tyto kroky platí jenom pro služby App Services spuštěné ve Windows.)

> [!NOTE]
> AI-Agent.xml a soubor agent jar by měly být ve stejné složce. Jsou často umístěny `/resources` společně ve složce projektu.  

#### <a name="enable-w3c-distributed-tracing"></a>Povolit distribuované trasování W3C

Do souboru AI-Agent.xml přidejte následující:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> Režim zpětné kompatibility je ve výchozím nastavení povolen a parametr enableW3CBackCompat je volitelný a měl by být používán pouze v případě, že jej chcete vypnout. 

V ideálním případě by tomu tak bylo v případě, kdy byly všechny vaše služby aktualizovány na novější verzi sad SDK podporujících protokol W3C. Důrazně doporučujeme přejít na novější verzi sad SDK s podporou W3C co nejdříve.

Ujistěte se, že ** [příchozí](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) i odchozí (agent) konfigurace** jsou přesně stejné.

## <a name="view-the-data"></a>Zobrazení dat
V prostředku Application Insights se agregované vzdálené závislosti a doby spuštění metody zobrazí [pod dlaždicí Výkon][metrics].

Chcete-li vyhledat jednotlivé instance sestav závislostí, výjimek a metod, otevřete [možnost Hledat][diagnostic].

[Diagnostika problémů se závislostmi – další informace](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Máte dotazy? Problémy?
* Žádná data? [Nastavení výjimek brány firewall](../../azure-monitor/app/ip-addresses.md)
* [Řešení potíží Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
