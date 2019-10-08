---
title: Prozkoumejte protokoly trasování Java v Azure Application Insights | Microsoft Docs
description: Prohledat Log4J nebo Logback trasování v Application Insights
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/18/2019
ms.author: mbullwin
ms.openlocfilehash: 99a18125fa5ce07a46fcc1af554fd03746ebf110
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038147"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Prozkoumejte protokoly trasování Java v Application Insights
Pokud pro trasování používáte Logback nebo Log4J (v 1.2 nebo v 2.0), můžete mít k dispozici automatické odesílání protokolů trasování, které Application Insights, kde je můžete prozkoumat a vyhledat.

> [!TIP]
> Pro vaši aplikaci stačí nastavit Application Insights klíč instrumentace. Pokud používáte rozhraní, jako je Java pružina, možná jste už klíč zaregistrovali jinde v konfiguraci vaší aplikace.

## <a name="using-the-application-insights-java-agent"></a>Použití Application Insightsho agenta Java

Můžete nakonfigurovat Application Insights agenta Java pro automatické zachycení protokolů tím, že povolíte funkci v souboru `AI-Agent.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">
         <Logging enabled="true" />
      </BuiltIn>
   </Instrumentation>
   <AgentLogger />
</ApplicationInsightsAgent>
```

Případně můžete postupovat podle následujících pokynů.

## <a name="install-the-java-sdk"></a>Instalace sady Java SDK

Podle pokynů nainstalujte [sadu Application Insights SDK pro jazyk Java][java], pokud jste to ještě neudělali.

## <a name="add-logging-libraries-to-your-project"></a>Přidání knihoven protokolování do projektu
*Zvolte vhodný způsob pro váš projekt.*

#### <a name="if-youre-using-maven"></a>Pokud používáte Maven...
Pokud je váš projekt již nastaven na použití Maven pro sestavení, slučte jeden z následujících fragmentů kódu do souboru pom. XML.

Pak aktualizujte závislosti projektu a Stáhněte si stažené soubory.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v 2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v 1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Pokud používáte Gradle...
Pokud je váš projekt již nastaven na použití Gradle pro sestavení, přidejte do skupiny `dependencies` v souboru Build. Gradle jeden z následujících řádků:

Pak aktualizujte závislosti projektu a Stáhněte si stažené soubory.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v 2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v 1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>V opačném případě...
Postupujte podle pokynů pro ruční instalaci Application Insights Java SDK, Stáhněte si jar (po obdržíní na stránce pro stažení klikněte na odkaz "Jar") pro příslušný příkaz append a přidejte do projektu stažený JAR pro přidání.

| Nástroj | Ke stažení | Knihovna |
| --- | --- | --- |
| Logback |[JAR pro Logback připojovat](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |ApplicationInsights-Logging-logback |
| Log4J v 2.0 |[Log4J v2 – JAR pro připojení](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |ApplicationInsights-Logging-log4j2 |
| Log4j v 1.2 |[Log4J v 1.2 – JAR pro přidávání](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |ApplicationInsights-Logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>Přidání připojení do protokolovacího rozhraní
Chcete-li začít získávat trasování, slučte příslušný fragment kódu do konfiguračního souboru Log4J nebo Logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v 2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v 1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Na Application Insights appender lze odkazovat pomocí nakonfigurovaného protokolovacího nástroje a nemusí nutně být kořenovým protokolovacím nástrojem (jak je uvedeno výše v ukázkách kódu).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Prozkoumejte trasování na portálu Application Insights
Teď, když jste nakonfigurovali projekt pro odesílání trasování do Application Insights, můžete zobrazit a vyhledat tato trasování na portálu Application Insights v okně [hledání][diagnostic] .

Výjimky odeslané prostřednictvím protokolovacích nástrojů se zobrazí na portálu jako telemetrie výjimek.

![Na portálu Application Insights otevřete hledání.](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Další kroky
[Diagnostické hledání][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


