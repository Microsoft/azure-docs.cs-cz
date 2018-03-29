---
title: Monitorování výkonu pro webové aplikace v jazyce Java ve službě Azure Application Insights | Microsoft Docs
description: Rozšířené výkon a sledování využití vašeho webu Java pomocí Application Insights.
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: b327e7f062cdf3e6b1b34a9540461dcb18caf21c
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitorování závislostí, zachycení výjimky a časy spuštění metoda v webové aplikace v jazyce Java


Pokud máte [instrumentovány webové aplikace Java pomocí Application Insights][java], můžete agenta Java a získejte detailnější přehledy, beze změn kódu:

* **Závislosti:** Data o volání, které vytváří vaše aplikace pro jiné komponenty, včetně:
  * **Volání REST** provedené prostřednictvím HttpClient, OkHttp a RestTemplate (pružiny) zaznamenání.
  * **Redis** zaznamenání volání provedená prostřednictvím Jedis klienta.
  * **[Volání JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**  -MySQL, SQL Server a Oracle DB příkazy jsou ukládány automaticky. Pro databázi MySQL Pokud volání trvá déle, než 10s, bude agent podřízen plán dotazu.
* **Zachycení výjimky:** informace o výjimky, které jsou zpracovávány vašeho kódu.
* **Metoda čas spuštění:** informace o době potřebné k provedení konkrétních metod.

Pokud chcete používat agenta Java, nainstalujte na vašem serveru. Webové aplikace musí být instrumentovány s [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Nainstalujte agenta Application Insights pro jazyk Java
1. Na počítači se systémem vašeho serveru Java, [stáhnout agenta](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Ověřte, zda Pokud chcete stáhnout stejné verson agenta Java jako balíčky jádra a web Application Insights Java SDK.
2. Upravte skript spuštění serveru aplikace a přidejte následující JVM:
   
    `javaagent:`*Úplná cesta k souboru JAR agenta*
   
    Například v Tomcat na počítač s Linuxem:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Restartujte server aplikace.

## <a name="configure-the-agent"></a>Konfiguraci agenta
Vytvořte soubor s názvem `AI-Agent.xml` a umístěte ji ve stejné složce jako soubor JAR agenta.

Nastavte obsah souboru xml. Podle následujícího příkladu lze zahrnout nebo vynechejte funkcí, že chcete upravte.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

Je nutné povolit výjimku sestavy a metoda časování pro jednotlivé metody.

Ve výchozím nastavení `reportExecutionTime` hodnotu true a `reportCaughtExceptions` je false.

## <a name="view-the-data"></a>Zobrazení dat
V prostředku Application Insights se zobrazí agregovaný vzdálené závislostí a metoda provádění časy [v rámci dlaždice výkon][metrics].

Chcete-li vyhledat jednotlivých instancí, závislostí, výjimek a metoda sestavy, otevřete [vyhledávání][diagnostic].

[Diagnostika problémů závislostí - Další](app-insights-asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Máte dotazy? Problémy?
* Žádná data? [Sada výjimky brány firewall](app-insights-ip-addresses.md)
* [Řešení potíží s Javou](app-insights-java-troubleshoot.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
