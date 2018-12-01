---
title: Prozkoumejte protokoly trasování ve službě Azure Application Insights Java | Dokumentace Microsoftu
description: Hledání Log4J nebo Logback trasování v Application Insights
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: mbullwin
ms.openlocfilehash: 934d5929048627bf378f50814f0cecde794ddab9
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727847"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Prozkoumejte protokoly trasování v Application Insights Java
Pokud již používáte, Logback nebo Log4J (verze 1.2 nebo 2.0) pro trasování, může mít protokoly trasování automaticky odesílaných do Application Insights, kde můžete prozkoumat a hledat v nich.

## <a name="install-the-java-sdk"></a>Instalace sady Java SDK

Postupujte podle pokynů k instalaci [Application Insights SDK pro Javu][java], pokud jste již tak neučinili.

## <a name="add-logging-libraries-to-your-project"></a>Přidání protokolování knihovny do projektu
*Zvolte vhodný způsob pro váš projekt.*

#### <a name="if-youre-using-maven"></a>Pokud používáte Maven...
Pokud váš projekt je již nastaven na sestavení s použitím nástroje Maven, slučte jednu z následujících fragmentů kódu do souboru pom.xml.

Pak obnovte závislosti projektu, k získání stažených binárních souborů.

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

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Verze 1.2 Log4J*

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
Pokud váš projekt je již nastaven na použití Gradle pro sestavení, přidejte jeden z následujících řádků `dependencies` skupinu v souboru build.gradle:

Pak obnovte závislosti projektu, k získání stažených binárních souborů.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Verze 1.2 Log4J**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>V opačném případě...
Postupujte podle pokynů k ručně nainstalovat sadu Application Insights Java SDK, stažení soubor jar pro příslušné appenderu protokolovacího (po posuzování na stránce Centrální Maven klikněte na odkaz "jar" v části stažení) a přidejte do projektu soubor jar stažené appenderu protokolovacího.

| Protokolovací nástroj | Ke stažení | Knihovna |
| --- | --- | --- |
| Logback |[Appenderu protokolovacího Logback Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights. protokolování logback |
| Log4J v2.0 |[Appenderu protokolovacího v2 Log4J Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Verze 1.2 Log4j |[Appenderu protokolovacího v1.2 Log4J Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>Přidat appenderu protokolovacího protokolovacího rozhraní
Jak začít využívat trasování, sloučí relevantní fragment kódu do konfiguračního souboru Log4J nebo Logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Verze 1.2 Log4J*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Appenders Application Insights může odkazovat všechny nakonfigurovaného protokolovacího nástroje a ne nutně protokolovacího nástroje root (jak je znázorněno výše uvedené ukázky kódu).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Prozkoumat trasování na portálu Application Insights
Teď, když jste nakonfigurovali projekt k odesílání trasování do Application Insights, můžete zobrazit a hledání v těchto trasování v portálu služby Application Insights [hledání] [ diagnostic] okno.

Odeslán výjimky prostřednictvím protokolovacích nástrojů se zobrazí na portálu jako Telemetrie výjimek.

![Na portálu Application Insights otevřete hledání](./media/app-insights-java-trace-logs/10-diagnostics.png)

## <a name="next-steps"></a>Další postup
[Diagnostické vyhledávání][diagnostic]

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md


