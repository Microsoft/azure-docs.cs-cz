---
title: Monitorování výkonu webových aplikací Java – Azure Application Insights
description: Rozšířené monitorování výkonu a využití vašeho webu Java pomocí Application Insights.
ms.topic: conceptual
ms.date: 01/10/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: c753e4e254890f9198da9bc913b29bdaae335b78
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573838"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitorování závislostí, zachycených výjimek a metod doby provádění ve webových aplikacích Java

> [!IMPORTANT]
> Přístup popsaný v tomto dokumentu se už nedoporučuje.
>
> Doporučený postup pro monitorování aplikací Java je použití automatické instrumentace beze změny kódu. Postupujte prosím podle pokynů pro [Application Insights agenta Java 3,0](./java-in-process-agent.md).

Pokud jste si nastavili [webovou aplikaci v jazyce Java pomocí Application Insights SDK][java], můžete k získání hlubších přehledů použít agenta Java, aniž byste museli měnit kód:

* **Závislosti:** Data o voláních, která vaše aplikace vytváří na jiné komponenty, včetně:
  * Budou zachycena **odchozí volání http** prostřednictvím Apache HttpClient, OkHttp a `java.net.HttpURLConnection` .
  * **Redis volání** prostřednictvím klienta Jedis.
  * **Dotazy JDBC** – pro MySQL a PostgreSQL, pokud volání trvá déle než 10 sekund, agent nahlásí plán dotazu.

* **Protokolování aplikace:** Zachycení a korelace protokolů aplikací pomocí požadavků HTTP a další telemetrie
  * **Log4j 1,2**
  * **Log4j2**
  * **Logback**

* **Lepší pojmenovávání operací:** (používá se pro agregaci požadavků na portálu)
  * Na **jaře** `@RequestMapping` .
  * **Jax-RS** založené na `@Path` . 

Chcete-li použít agenta Java, nainstalujte jej na server. Webové aplikace musí být instrumentované pomocí [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instalace agenta Application Insights pro jazyk Java
1. V počítači, na kterém běží Java Server, [Stáhněte agenta 2. x](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/2.6.2). Ujistěte se, že verze agenta 2. x, který používáte, odpovídá verzi 2. x Application Insights Java SDK, kterou používáte.
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

Klíč: `JAVA_OPTS` hodnota: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.6.2.jar`

Agent musí být zabalen jako prostředek v projektu tak, že končí na D:/Home/site/wwwroot/Directory. Můžete potvrdit, že je váš agent ve správném adresáři App Service, a to tak, že kliknete na **vývojové nástroje**  >  **Pokročilé nástroje**  >  **ladit konzolu** a prozkoumáte obsah adresáře webu.    

* Kliknutím na Uložit uložte nastavení a kliknutím na Restartovat restartujte aplikaci. (Tyto kroky platí jenom pro App Services spuštěné v systému Windows.)

> [!NOTE]
> AI-Agent.xml a soubor JAR agenta by měly být ve stejné složce. Jsou často umístěny dohromady do `/resources` složky projektu.  

#### <a name="enable-w3c-distributed-tracing"></a>Povolit distribuované trasování W3C

Přidejte následující AI-Agent.xml:

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

Ujistěte se, že **konfigurace [příchozího](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) i odchozího (agentu)** jsou přesně stejné.

## <a name="view-the-data"></a>Zobrazení dat
V prostředku Application Insights se na [dlaždici výkon][metrics]zobrazí agregovaná doba spuštění pro vzdálenou závislost a metodu.

Chcete-li vyhledat jednotlivé instance sestav závislostí, výjimek a metod, otevřete [hledání][diagnostic].

[Diagnostikování problémů se závislostmi – Další informace](./asp-net-dependencies.md#diagnosis)

## <a name="questions-problems"></a>Máte otázky? Problémy?
* Žádná data? [Nastavení výjimek brány firewall](./ip-addresses.md)
* [Řešení potíží Java](java-troubleshoot.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md

