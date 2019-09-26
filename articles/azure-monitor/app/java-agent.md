---
title: Sledování výkonu pro webové aplikace v jazyce Java v Azure Application Insights | Microsoft Docs
description: Rozšířené monitorování výkonu a využití vašeho webu Java pomocí Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: af157204ad1e1b28639ae2d8f192b3122afa8147
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299232"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitorování závislostí, zachycených výjimek a metod doby provádění ve webových aplikacích Java


Pokud jste si nastavili [svou webovou aplikaci v jazyce Java pomocí Application Insights][java], můžete použít agenta Java k získání hlubších přehledů, aniž byste museli měnit kód:

* **Závislosti** Data o voláních, která vaše aplikace vytváří na jiné komponenty, včetně:
  * Budou zachycena **odchozí volání http** prostřednictvím Apache HttpClient, OkHttp `java.net.HttpURLConnection` a.
  * **Redis volání** prostřednictvím klienta Jedis.
  * **Dotazy JDBC** – pro MySQL a PostgreSQL, pokud volání trvá déle než 10 sekund, agent nahlásí plán dotazu.

* **Protokolování aplikace:** Zachycení a korelace protokolů aplikací pomocí požadavků HTTP a další telemetrie
  * **Log4j 1,2**
  * **Log4j2**
  * **Logback**

* **Lepší pojmenovávání operací:** (používá se pro agregaci požadavků na portálu)
  * Na`@RequestMapping`jaře.
  * **Jax-RS** založené na `@Path`. 

Chcete-li použít agenta Java, nainstalujte jej na server. Webové aplikace musí být instrumentované pomocí [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instalace agenta Application Insights pro jazyk Java
1. Na počítači s vaším serverem Java si [stáhněte agenta](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Nezapomeňte si stáhnout stejnou verzi agenta Java, jako je verze základních a webových balíčků sady Application Insights Java SDK.
2. Upravte spouštěcí skript aplikačního serveru a přidejte následující argument JVM:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Například v Tomcat na počítači se systémem Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Restartujte aplikační server.

## <a name="configure-the-agent"></a>Konfigurace agenta
Vytvořte soubor s názvem `AI-Agent.xml` a umístěte ho do stejné složky jako soubor JAR agenta.

Nastavte obsah souboru XML. Úpravou následujícího příkladu zahrnete nebo vynecháte požadované funkce.

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

## <a name="additional-config-spring-boot"></a>Dodatečná konfigurace (jarní spuštění)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

V případě služby Azure App Services postupujte následovně:

* Klikněte na Nastavení > Nastavení aplikace.
* V části Nastavení aplikace přidejte novou dvojici klíče a hodnoty:

Zkrat `JAVA_OPTS`Osa`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

Nejnovější verzi agenta [Java najdete tady](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

Agent musí být zabalen jako prostředek v projektu tak, že končí na D:/Home/site/wwwroot/Directory. Můžete potvrdit, že je váš agent ve správném adresáři App Service, a to tak, že kliknete na **vývojové nástroje** > **Pokročilé nástroje** > **ladit konzolu** a prozkoumáte obsah adresáře webu.    

* Uložte nastavení a restartujte aplikaci. (Tyto kroky platí jenom pro App Services spuštěné v systému Windows.)

> [!NOTE]
> AI-Agent. XML a soubor JAR agenta by měly být ve stejné složce. Jsou často umístěny dohromady do `/resources` složky projektu.  

#### <a name="enable-w3c-distributed-tracing"></a>Povolit distribuované trasování W3C

Do souboru AI-Agent. xml přidejte následující:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> Režim zpětné kompatibility je ve výchozím nastavení povolený a parametr enableW3CBackCompat je nepovinný a měl by se používat jenom v případě, že ho chcete vypnout. 

V ideálním případě se jedná o případ, kdy byly všechny služby aktualizovány na novější verzi sady SDK podporující protokol W3C. Důrazně doporučujeme přesunout na novější verzi sad SDK s podporou konsorcia W3C, jakmile to bude možné.

Ujistěte se, že **konfigurace [příchozího](correlation.md#w3c-distributed-tracing) i odchozího (agentu)** jsou přesně stejné.

## <a name="view-the-data"></a>Zobrazení dat
V prostředku Application Insights se na [dlaždici výkon][metrics]zobrazí agregovaná doba spuštění pro vzdálenou závislost a metodu.

Chcete-li vyhledat jednotlivé instance sestav závislostí, výjimek a metod, otevřete [hledání][diagnostic].

[Diagnostikování problémů se závislostmi – Další informace](../../azure-monitor/app/asp-net-dependencies.md#diagnosis)

## <a name="questions-problems"></a>Máte dotazy? Problémy?
* Žádná data? [Nastavení výjimek brány firewall](../../azure-monitor/app/ip-addresses.md)
* [Řešení potíží s Javou](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
