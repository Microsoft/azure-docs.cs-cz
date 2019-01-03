---
title: Sledování výkonu webové aplikace v Javě ve službě Azure Application Insights | Dokumentace Microsoftu
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
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: c3d715fbab082355f702101762a8fde671347de1
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53980672"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitorování závislostí, zachycené výjimky a časy spuštění metody do webové aplikace v Javě


Pokud máte [instrumentována webové aplikace Java pomocí Application Insights][java], agenta Java můžete získat podrobnější přehledy, bez jakýchkoli změn kódu:

* **Závislosti:** Data o volání, které aplikace provádí na dalších komponentách, včetně:
  * **Volání REST** provádí přes HttpClient, OkHttp a RestTemplate (Spring) jsou zachyceny.
  * **Redis** volání provedená prostřednictvím klienta Jedis jsou zachyceny.
  * **[Volání JDBC](https://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**  -MySQL a SQL Server, Oracle DB příkazy ukládány automaticky. Pro MySQL Pokud volání trvá déle než 10s, bude agent podřízen plán dotazu.
* **Zachycené výjimky:** Informace o výjimkách, které váš kód zpracovává.
* **Metoda doba spuštění:** Informace o čas potřebný k provedení specifických metod.

Pokud chcete používat agenta Java, nainstalujte na server. Svoje webové aplikace musí být neinstrumentují službou [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instalace agenta služby Application Insights pro Java
1. Na počítači spuštěný serveru Java [stáhnout agenta](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Zkontrolujte, zda Pokud chcete stáhnout stejné verson agenta Java jako balíčky core a web Application Insights Java SDK.
2. Upravte spouštěcí skript serveru aplikace a přidejte následující JVM:
   
    `javaagent:`*Úplná cesta k souboru JAR agenta*
   
    Například v Tomcatu na počítači s Linuxem:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Restartujte server aplikace.

## <a name="configure-the-agent"></a>Konfigurace agenta
Vytvořte soubor s názvem `AI-Agent.xml` a umístěte ho ve stejné složce jako soubor JAR agenta.

Nastavení obsahu souboru xml. Podle následujícího příkladu lze zahrnout nebo vynechejte funkce, že chcete upravte.

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

Je nutné povolit sestavy výjimku a metoda časování pro jednotlivé metody.

Ve výchozím nastavení `reportExecutionTime` má hodnotu true a `reportCaughtExceptions` má hodnotu false.

## <a name="view-the-data"></a>Zobrazení dat
V prostředku Application Insights se zobrazí agregovaná vzdálené závislosti a způsob spuštění s úspěšností [pod dlaždice výkon][metrics].

Chcete-li vyhledat jednotlivé instance dané závislosti, výjimky a metoda sestavy, otevřete [hledání][diagnostic].

[Diagnostika závislostí problémy – Další informace](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

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
[metrics]: ../../application-insights/app-insights-metrics-explorer.md
