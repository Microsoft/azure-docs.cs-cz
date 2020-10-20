---
title: Monitorování aplikací Java kdekoli – Azure Monitor Application Insights
description: Monitorování výkonu aplikací bez kódu pro aplikace Java běžící v jakémkoli prostředí bez instrumentace aplikace. Najděte hlavní příčinu potíží d pomocí distribuovaného trasování a mapy aplikací.
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 36f2add41457d1d82b0efd6c6804496018c85225
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215259"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Možnosti konfigurace – samostatný agent Java pro Azure Monitor Application Insights



## <a name="connection-string-and-role-name"></a>Připojovací řetězec a název role

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

Připojovací řetězec je vyžadován a název role je důležitý vždy, když odesíláte data z různých aplikací do stejného Application Insights prostředku.

Další podrobnosti a další možnosti konfigurace najdete níže.

## <a name="configuration-file-path"></a>Cesta ke konfiguračnímu souboru

Ve výchozím nastavení Application Insights Java 3,0 Preview očekává, že se konfigurační soubor pojmenuje a že se `ApplicationInsights.json` nachází ve stejném adresáři jako `applicationinsights-agent-3.0.0-PREVIEW.5.jar` .

Vlastní cestu ke konfiguračnímu souboru můžete zadat buď pomocí

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` Proměnná prostředí nebo
* `applicationinsights.configurationFile` Systémová vlastnost Java

Pokud zadáte relativní cestu, bude vyřešena vzhledem k adresáři, kde `applicationinsights-agent-3.0.0-PREVIEW.5.jar` se nachází.

## <a name="connection-string"></a>Připojovací řetězec

To je povinné. Připojovací řetězec najdete v prostředku Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Připojovací řetězec Application Insights":::


```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Připojovací řetězec můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_CONNECTION_STRING` .

Při nastavení připojovacího řetězce se agent Java zakáže.

## <a name="cloud-role-name"></a>Název cloudové role

Název role cloudu se používá k označení součásti na mapě aplikace.

Pokud chcete nastavit název cloudové role:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Pokud není název cloudové role nastaven, použije se k označení součásti na mapě aplikace název Application Insights prostředku.

Název cloudové role můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_ROLE_NAME` .

## <a name="cloud-role-instance"></a>Instance cloudové role

Instance role cloudu je standardně nastavená na název počítače.

Pokud chcete nastavit instanci cloudové role na jinou hodnotu než název počítače:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

Instanci cloudové role můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_ROLE_INSTANCE` .

## <a name="application-log-capture"></a>Zachycení protokolu aplikace

Application Insights Java 3,0 Preview automaticky zachycuje protokolování aplikací prostřednictvím log4j, Logback a Java. util. Logging.

Ve výchozím nastavení bude zachytávání veškerého protokolování provedené na `INFO` úrovni nebo výše.

Pokud chcete změnit tuto prahovou hodnotu:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "WARN"
        }
      }
    }
  }
}
```

Prahovou hodnotu protokolování můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_LOGGING_THRESHOLD` .

Jedná se o platné `threshold` hodnoty, které můžete zadat v `ApplicationInsights.json` souboru a jak odpovídají úrovním protokolování napříč různými architekturami protokolování:

| prahová hodnota   | Log4j  | Logback | JUL     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| ZÁVAŽNÁ             | ZÁVAŽNÁ  | CHYBA   | VÁŽNOU  |
| Chyba (nebo ZÁVAŽNá) | CHYBA  | CHYBA   | VÁŽNOU  |
| UPOZORNIT (nebo upozornění) | UPOZORNIT   | UPOZORNIT    | WARNING |
| PŘÍJEMCE              | PŘÍJEMCE   | PŘÍJEMCE    | PŘÍJEMCE    |
| Konfigurace            | DEBUG  | DEBUG   | Konfigurace  |
| LADIT (nebo jemné)   | DEBUG  | DEBUG   | Nevadí    |
| LEPŠÍ             | DEBUG  | DEBUG   | LEPŠÍ   |
| TRACE (nebo nejlepší) | TRACE  | TRACE   | Nejlepší  |
| ALL               | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>JMX metriky

Pokud máte nějaké JMX metriky, které vás zajímají, zachytíte:

```json
{
  "instrumentationSettings": {
    "preview": {
      "jmxMetrics": [
        {
          "objectName": "java.lang:type=Runtime",
          "attribute": "Uptime",
          "display": "JVM uptime (millis)"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Metaspace",
          "attribute": "Usage.used",
          "display": "MetaSpace Used"
        }
      ]
    }
  }
}
```

Hodnoty metriky numeric a Boolean JMX jsou podporované. Logické JMX metriky jsou namapovány na `0` hodnotu false a `1` na hodnotu true.

[//]: # "Poznámka: tady se nedokumentuje APPLICATIONINSIGHTS_JMX_METRICS"
[//]: # "var Embedded ve formátu ENV je v podkladu a měl by se zdokumentovat jenom pro scénář připojení bez kódu."

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Mikroměřič (včetně metrik ze pružinového spouštěcího válce)

Pokud vaše aplikace používá [mikroměřič](https://micrometer.io), Application Insights 3,0 (počínaje verzí Preview. 2) nyní zachycuje metriky odesílané do globálního registru mikroměřiče.

Pokud vaše aplikace používá [pružinový spouštěcí systém](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html), Application Insights 3,0 (počínaje verzí Preview. 4) nyní zachycuje metriky nakonfigurované pomocí pružinového spouštěcího limitu (který používá mikroměřiče, ale nepoužívá globální Registry mikroměřiče).

Pokud chcete zakázat tyto funkce:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "micrometer": {
          "enabled": false
        }
      }
    }
  }
}
```

## <a name="heartbeat"></a>Tep

Ve výchozím nastavení Application Insights Java 3,0 Preview pošle metriku prezenčního signálu každých 15 minut. Pokud používáte metriku prezenčního signálu k aktivaci výstrah, můžete zvýšit frekvenci tohoto prezenčního signálu:

```json
{
  "instrumentationSettings": {
    "preview": {
      "heartbeat": {
        "intervalSeconds": 60
      }
    }
  }
}
```

> [!NOTE]
> Frekvence tohoto prezenčního signálu se nedá snížit, protože data prezenčního signálu se také používají ke sledování využití Application Insights.

## <a name="sampling"></a>Vzorkování

Vzorkování je užitečné, pokud potřebujete snížit náklady.
Vzorkování se provádí jako funkce na ID operace (označované také jako ID trasování), takže stejné ID operace bude mít vždycky stejné rozhodnutí o vzorkování. Tím zajistíte, že nebudete mít k disukázce části distribuované transakce v době, kdy ostatní části jsou ukázkové.

Pokud například nastavíte vzorkování na 10%, zobrazí se vám pouze 10% vašich transakcí, ale každá z těchto 10% bude mít kompletní podrobnosti transakce na konci.

Tady je příklad, jak nastavit vzorkování na **10% všech transakcí** – Ujistěte se prosím, že jste nastavili vzorkovací frekvenci, která je pro váš případ použití správná:

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10
        }
      }
    }
  }
}
```

Procentuální hodnotu vzorkování můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` .

## <a name="http-proxy"></a>Proxy server HTTP

Pokud je vaše aplikace za bránou firewall a nemůže se připojit přímo k Application Insights (viz [IP adresy, které používá Application Insights](./ip-addresses.md)), můžete nakonfigurovat Application Insights Java 3,0 Preview pro použití proxy serveru http:

```json
{
  "instrumentationSettings": {
    "preview": {
      "httpProxy": {
        "host": "myproxy",
        "port": 8080
      }
    }
  }
}
```

## <a name="self-diagnostics"></a>Samoobslužná Diagnostika

"Samoobslužná Diagnostika" odkazuje na interní protokolování z Application Insights Java 3,0 Preview.

To může být užitečné pro hledání a diagnostikování problémů Application Insights sebe sama.

Ve výchozím nastavení se protokoluje do konzoly s úrovní `warn` odpovídající této konfiguraci:

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "console",
        "level": "WARN"
      }
    }
  }
}
```

Platné úrovně jsou `OFF` , `ERROR` , `WARN` , `INFO` , a `DEBUG` `TRACE` .

Pokud se chcete přihlásit k souboru místo protokolování do konzoly:

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "file",
        "directory": "/var/log/applicationinsights",
        "level": "WARN",
        "maxSizeMB": 10
      }
    }
  }
}
```

Při použití protokolování souborů se po každém `maxSizeMB` pokusu o soubor změní a zachová se kromě aktuálního souboru protokolu jenom poslední dokončený soubor protokolu.
