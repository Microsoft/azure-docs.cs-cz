---
title: Strukturovaný aplikační protokol pro Azure jaře Cloud | Microsoft Docs
description: Tento článek vysvětluje, jak vygenerovat a shromažďovat strukturovaná data protokolu aplikací v Azure jaře cloudu.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 844530c6d1650b5fddd27b10c775c4364a3f5147
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877861"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Strukturovaný aplikační protokol pro jarní cloud Azure

Tento článek vysvětluje, jak vygenerovat a shromažďovat strukturovaná data protokolu aplikací v Azure jaře cloudu. Díky správné konfiguraci poskytuje Azure jarní Cloud užitečné dotazy a analýzy protokolů aplikací prostřednictvím Log Analytics.

## <a name="log-schema-requirements"></a>Požadavky schématu protokolu
Pro zlepšení prostředí dotazů protokolu musí být protokol aplikace ve formátu JSON a musí odpovídat schématu. Azure jaře Cloud používá toto schéma k analýze vaší aplikace a streamu do Log Analytics. 

**Požadavky na schéma JSON:**

| Klíč JSON      | Typ hodnoty JSON|  Vyžadováno | Sloupec v Log Analytics| Description |
| --------------| ------------|-----------|-----------------|--------------------------|
| časové razítko     | řetězec      |     Yes   | AppTimestamp    | časové razítko ve formátu UTC  |
| Nástroj        | řetězec      |     No    | Nástroj          | Nástroj                   |
| úroveň         | řetězec      |     No    | CustomLevel     | úroveň protokolování                |
| vlákno        | řetězec      |     No    | Thread          | vlákno                   |
| zpráva       | řetězec      |     No    | Zpráva         | Zpráva protokolu              |
| Trasování zásobníku    | řetězec      |     No    | Trasování zásobníku      | trasování zásobníku výjimek    |
| exceptionClass| řetězec      |     No    | ExceptionClass  | název třídy výjimky     |
| MDC           | vnořený kód JSON |     No    |                 | mapovaný kontext diagnostiky|
| MDC. traceId   | řetězec      |     No    | TraceId         |ID trasování pro distribuované trasování|
| MDC. spanId    | řetězec      |     No    | SpanId          |ID rozsahu pro distribuované trasování |
|               |             |           |                 |                          |

* Pole časové razítko je povinné a mělo by být ve formátu UTC, všechna ostatní pole jsou volitelná.
* pro účely trasování se používají "traceId" a "spanId" v poli "MDC".
* Protokolujte každý záznam JSON na jednom řádku. 

**Ukázka záznamu protokolu** 
 ```
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="generate-schema-compliant-json-log"></a>Generovat protokol JSON kompatibilní se schématy  
U jarních aplikací můžete vygenerovat očekávaný formát protokolu JSON pomocí běžných [protokolovacích rozhraní](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration), například [logback](http://logback.qos.ch/) a [log4j2](https://logging.apache.org/log4j/2.x/). 

### <a name="log-with-logback"></a>Protokolovat pomocí logback 
Při použití spouštěcích startů pružiny se ve výchozím nastavení používá logback. Pro aplikace logback použijte [logstash-Encoder](https://github.com/logstash/logstash-logback-encoder) k vygenerování protokolu ve formátu JSON. Tato metoda je podporována ve jarním spuštění verze 2.1 +. 

Postup:

1. Přidejte závislost logstash do souboru pom.xml. 

    ```json
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```
1. Aktualizujte soubor logback.xml config tak, aby se nastavil formát JSON.
    ```json
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <timestamp>
                    <fieldName>timestamp</fieldName>
                    <timeZone>UTC</timeZone>
                </timestamp>
                <loggerName>
                    <fieldName>logger</fieldName>
                </loggerName>
                <logLevel>
                    <fieldName>level</fieldName>
                </logLevel>
                <threadName>
                    <fieldName>thread</fieldName>
                </threadName>
                <nestedField>
                    <fieldName>mdc</fieldName>
                    <providers>
                        <mdc/>
                    </providers>
                </nestedField>
                <stackTrace>
                    <fieldName>stackTrace</fieldName>
                </stackTrace>
                <message/>
                <throwableClassName>
                    <fieldName>exceptionClass</fieldName>
                </throwableClassName>
            </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout"/>
        </root>
    </configuration>
    ```

### <a name="log-with-log4j2"></a>Protokolovat pomocí log4j2 

Pro aplikace log4j2 použijte [JSON-Template-layout](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html) k vygenerování protokolu ve formátu JSON. Tato metoda je podporována ve jarním spuštění verze 2.1 +.

Postup:

1. Vylučte `spring-boot-starter-logging` z `spring-boot-starter` , přidejte `spring-boot-starter-log4j2` závislosti `log4j-layout-template-json` do souboru pom.xml.

    ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                    <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-layout-template-json</artifactId>
            <version>2.14.0</version>
        </dependency>
    ```

2. Připravte soubor šablony rozložení JSON jsonTemplate.jsve vaší cestě třídy.

    ```json
        {
            "mdc": {
                "$resolver": "mdc"
            },
            "exceptionClass": {
                "$resolver": "exception",
                "field": "className"
            },
            "stackTrace": {
                "$resolver": "exception",
                "field": "stackTrace",
                "stringified": true
            },
            "message": {
                "$resolver": "message",
                "stringified": true
            },
            "thread": {
                "$resolver": "thread",
                "field": "name"
            },
            "timestamp": {
                "$resolver": "timestamp",
                "pattern": {
                    "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                    "timeZone": "UTC"
                }
            },
            "level": {
                "$resolver": "level",
                "field": "name"
            },
            "logger": {
                "$resolver": "logger",
                "field": "name"
            }
        }
    ```

3. Tuto šablonu rozložení JSON použijte v souboru config log4j2.xml. 

    ```json
        <configuration>
            <appenders>
                <console name="Console" target="SYSTEM_OUT">
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json"/>
                </console>
            </appenders>
            <loggers>
                <root level="info">
                <appender-ref ref="Console"/>
                </root>
            </loggers>
        </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>Analyzovat protokoly v Log Analytics

Po správném nastavení aplikace bude protokol konzoly aplikace streamovaná Log Analytics. Struktura umožňuje efektivní dotazování v Log Analytics.

### <a name="check-log-structure-in-log-analytics"></a>Kontrolovat strukturu protokolu v Log Analytics
Použijte následující postup:
1. Přejít na stránku Přehled služby instance služby.
2. Klikněte na `Logs` položku v části `Monitoring` .
3. Spusťte tento dotaz.

   ```
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. Protokoly aplikací vrátí, jak je znázorněno na následujícím obrázku:

![Zobrazit protokol JSON](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>Zobrazit položky protokolu obsahující chyby

Chcete-li zkontrolovat položky protokolu, které obsahují chybu, spusťte následující dotaz:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR" 
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName 
| sort by AppTimestamp
```

Pomocí tohoto dotazu můžete najít chyby nebo změnit výrazy dotazu tak, aby se vyhledala specifická Třída výjimky nebo kód chyby. 

### <a name="show-log-entries-for-a-specific-traceid"></a>Zobrazit položky protokolu pro konkrétní traceId
Chcete-li zkontrolovat položky protokolu pro konkrétní ID trasování "trace_id", spusťte následující dotaz:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id" 
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName 
| sort by AppTimestamp
```

## <a name="next-steps"></a>Další kroky
* Další informace o dotazu protokolu najdete [v tématu Začínáme s dotazy protokolu v Azure monitor](../azure-monitor/logs/get-started-queries.md)