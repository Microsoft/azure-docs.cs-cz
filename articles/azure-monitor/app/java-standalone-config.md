---
title: Monitorování aplikací Java kdekoli – Azure Monitor Application Insights
description: Monitorování výkonu aplikací bez kódu pro aplikace Java běžící v jakémkoli prostředí bez instrumentace aplikace. Najděte hlavní příčinu potíží d pomocí distribuovaného trasování a mapy aplikací.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 5d930d349a2ab1efbd7a61904874bf6bdb411889
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641885"
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

Ve výchozím nastavení Application Insights Java 3,0 Preview očekává, že se konfigurační soubor pojmenuje `ApplicationInsights.json`a že se nachází ve stejném adresáři jako `applicationinsights-agent-3.0.0-PREVIEW.jar`.

Vlastní cestu ke konfiguračnímu souboru můžete zadat buď pomocí

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`Proměnná prostředí nebo
* `applicationinsights.configurationFile`Systémová vlastnost Java

Pokud zadáte relativní cestu, bude vyřešena vzhledem k adresáři, kde `applicationinsights-agent-3.0.0-PREVIEW.jar` se nachází.

## <a name="connection-string"></a>Připojovací řetězec

To je povinné. Připojovací řetězec najdete v prostředku Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Připojovací řetězec Application Insights":::

Připojovací řetězec můžete také nastavit pomocí proměnné `APPLICATIONINSIGHTS_CONNECTION_STRING`prostředí.

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

Název cloudové role můžete také nastavit pomocí proměnné `APPLICATIONINSIGHTS_ROLE_NAME`prostředí.

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

Instanci cloudové role můžete také nastavit pomocí proměnné `APPLICATIONINSIGHTS_ROLE_INSTANCE`prostředí.

## <a name="application-log-capture"></a>Zachycení protokolu aplikace

Application Insights Java 3,0 Preview automaticky zachycuje protokolování aplikací prostřednictvím log4j, Logback a Java. util. Logging.

Ve výchozím nastavení bude zachytávání veškerého protokolování `WARN` provedené na úrovni nebo výše.

Pokud chcete změnit tuto prahovou hodnotu:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "ERROR"
        }
      }
    }
  }
}
```

Jedná se o platné `threshold` hodnoty, které můžete zadat v `ApplicationInsights.json` souboru a jak odpovídají úrovním protokolování napříč různými architekturami protokolování:

| `threshold`  | Log4j  | Logback | JUL     |
|--------------|--------|---------|---------|
| OFF          | OFF    | OFF     | OFF     |
| ZÁVAŽNÁ        | ZÁVAŽNÁ  | ERROR   | VÁŽNOU  |
| CHYBA/ZÁVAŽNÁ | ERROR  | ERROR   | VÁŽNOU  |
| UPOZORNĚNÍ A UPOZORNĚNÍ | UPOZORNIT   | UPOZORNIT    | WARNING |
| PŘÍJEMCE         | PŘÍJEMCE   | PŘÍJEMCE    | PŘÍJEMCE    |
| Konfigurace       | DEBUG  | DEBUG   | Konfigurace  |
| LADĚNÍ/JEMNÉ   | DEBUG  | DEBUG   | Nevadí    |
| LEPŠÍ        | DEBUG  | DEBUG   | LEPŠÍ   |
| TRACE/NEJLEPŠÍ | TRACE  | TRACE   | Nejlepší  |
| VŠE          | VŠE    | VŠE     | VŠE     |

## <a name="jmx-metrics"></a>JMX metriky

Pokud máte nějaké JMX metriky, které vás zajímají, zachytíte:

```json
{
  "instrumentationSettings": {
    "preview": {
        "jmxMetrics": [
        {
          "objectName": "java.lang:type=ClassLoading",
          "attribute": "LoadedClassCount",
          "display": "Loaded Class Count"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Code Cache",
          "attribute": "Usage.used",
          "display": "Code Cache Used"
        }
      ]
    }
  }
}
```

## <a name="micrometer"></a>Mikrometr

Ve výchozím nastavení, pokud vaše aplikace používá [mikroměřič](https://micrometer.io), Application Insights 3,0 (počínaje verzí Preview. 2) nyní přidá sám sebe do globálního registru mikroměřiče a zachytí metriky mikroměřiče.

Pokud chcete zakázat tuto funkci:

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

## <a name="heartbeat"></a>Prezenční signál

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

## <a name="http-proxy"></a>Proxy server HTTP

Pokud je vaše aplikace za bránou firewall a nemůže se připojit přímo k Application Insights (viz [IP adresy, které používá Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)), můžete nakonfigurovat Application Insights Java 3,0 Preview pro použití proxy serveru http:

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

Ve výchozím nastavení se protokoluje do konzoly s `warn`úrovní odpovídající této konfiguraci:

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

Platné úrovně jsou `OFF`, `ERROR`, `WARN`, `INFO`, `DEBUG`a `TRACE`.

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

Při použití protokolování souborů se po každém pokusu `maxSizeMB`o soubor změní a zachová se kromě aktuálního souboru protokolu jenom poslední dokončený soubor protokolu.
