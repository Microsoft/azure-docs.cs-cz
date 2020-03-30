---
title: Prozkoumejte protokoly trasování v Jazyce java v Azure Application Insights
description: Hledání trasování Log4J nebo Přihlášení v application insights
ms.topic: conceptual
ms.date: 05/18/2019
ms.openlocfilehash: da1b76d52ab93f4d1be7196d6eb7286579481119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657210"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Prozkoumejte protokoly trasování v jazyce Java v application insights
Pokud používáte Logback nebo Log4J (v1.2 nebo v2.0) pro trasování, můžete si nechat protokoly trasování automaticky odeslat do Application Insights, kde je můžete prozkoumat a prohledat.

> [!TIP]
> Pro vaši aplikaci je třeba nastavit klíč instrumentace Application Insights pouze jednou. Pokud používáte architekturu, jako je Java Spring, možná jste již zaregistrovali klíč jinde v konfiguraci vaší aplikace.

## <a name="using-the-application-insights-java-agent"></a>Použití agenta Java Application Insights

Ve výchozím nastavení agent Jazyka Java Application `WARN` Insights automaticky zachycuje protokolování prováděné na úrovni a vyšší úrovni.

Můžete změnit prahovou hodnotu protokolování, `AI-Agent.xml` která je zachycena pomocí souboru:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging threshold="info"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

Protokolování protokolování agenta Java můžete `AI-Agent.xml` zakázat pomocí souboru:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging enabled="false"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>Alternativně (na rozdíl od použití java agenta) můžete postupovat podle níže uvedených pokynů

### <a name="install-the-java-sdk"></a>Instalace sady Java SDK

Podle pokynů nainstalujte [aplikaci Insights SDK pro Javu][java], pokud jste to ještě neudělali.

### <a name="add-logging-libraries-to-your-project"></a>Přidání knihoven protokolování do projektu
*Zvolte vhodný způsob pro váš projekt.*

#### <a name="if-youre-using-maven"></a>Pokud používáte Maven...
Pokud je projekt již nastaven na použití maven pro sestavení, sloučit jeden z následujících úryvků kódu do souboru pom.xml.

Potom aktualizujte závislosti projektu, abyste získali soubory binárních souborů.

*Přihlašování*

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

*Log4J v1.2*

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
Pokud je projekt již nastaven na použití Gradle pro sestavení, `dependencies` přidejte do skupiny v souboru build.gradle jeden z následujících řádků:

Potom aktualizujte závislosti projektu, abyste získali soubory binárních souborů.

**Přihlašování**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>V opačném případě...
Postupujte podle pokynů pro ruční instalaci Application Insights Java SDK, stáhněte si nádobu (Po příjezdu na Maven Central Page klikněte na odkaz "jar" v sekci stažení) pro příslušný dodatek a přidejte staženou nádobu s přídavkem do projektu.

| Logger | Stáhnout | Knihovna |
| --- | --- | --- |
| Přihlašování |[Logback appender Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-log-logback |
| Log4J v2.0 |[Log4J v2 přívěsk jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j v1.2 |[Log4J v1.2 přívěsk Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>Přidání přidávače do rámce protokolování
Chcete-li začít získat trasování, sloučit příslušný fragment kódu do konfiguračního souboru Log4J nebo Logback: 

*Přihlašování*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

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

*Log4J v1.2*

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

App Insights appenders může odkazovat libovolný nakonfigurovaný protokolovací nástroj, a ne nutně root logger (jak je znázorněno na ukázkách kódu výše).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Prozkoumejte své stopy na portálu Application Insights
Teď, když jste nakonfigurovali projekt pro odesílání trasování do Application Insights, můžete zobrazit a prohledat tyto trasování na portálu Application Insights v okně [Hledat.][diagnostic]

Výjimky odeslané prostřednictvím úhozů kláves se zobrazí na portálu jako výjimka telemetrie.

![Na portálu Application Insights otevřete Search](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Další kroky
[Diagnostické vyhledávání][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


