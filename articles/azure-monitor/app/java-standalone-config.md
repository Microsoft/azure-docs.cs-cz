---
title: Monitorování aplikací java kdekoli – Azure Monitor Application Insights
description: Monitorování výkonu bezkódových aplikací pro aplikace Java spuštěné v libovolném prostředí bez instrumentace aplikace. Najděte hlavní příčinu problémů d pomocí distribuovaného trasování a mapy aplikací.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 5d930d349a2ab1efbd7a61904874bf6bdb411889
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641885"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Možnosti konfigurace – samostatný agent Jazyka Java pro přehledy aplikací Azure Monitor



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

Připojovací řetězec je povinný a název role je důležitý při každém odesílání dat z různých aplikací do stejného prostředku Application Insights.

Další podrobnosti a další možnosti konfigurace naleznete níže, kde najdete další podrobnosti.

## <a name="configuration-file-path"></a>Cesta konfiguračního souboru

Ve výchozím nastavení aplikace Insights Java 3.0 Preview `ApplicationInsights.json`očekává, že konfigurační `applicationinsights-agent-3.0.0-PREVIEW.jar`soubor bude pojmenován a bude umístěn ve stejném adresáři jako .

Můžete zadat vlastní cestu konfiguračního souboru pomocí

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`proměnná prostředí, nebo
* `applicationinsights.configurationFile`Vlastnost systému Java

Pokud zadáte relativní cestu, bude vyřešena vzhledem `applicationinsights-agent-3.0.0-PREVIEW.jar` k adresáři, kde je umístěn.

## <a name="connection-string"></a>Připojovací řetězec

To je nutné. Připojovací řetězec najdete v prostředku Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Připojovací řetězec Přehledy aplikací":::

Připojovací řetězec můžete také `APPLICATIONINSIGHTS_CONNECTION_STRING`nastavit pomocí proměnné prostředí .

## <a name="cloud-role-name"></a>Název role cloudu

Název role cloudu se používá k označení komponenty na mapě aplikace.

Pokud chcete nastavit název role cloudu:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Pokud není nastaven název role cloudu, bude název prostředku Application Insights použit k označení komponenty na mapě aplikace.

Můžete také nastavit název role cloudu `APPLICATIONINSIGHTS_ROLE_NAME`pomocí proměnné prostředí .

## <a name="cloud-role-instance"></a>Instance role cloudu

Instance role cloudu je výchozí pro název počítače.

Pokud chcete nastavit instanci role cloudu na něco jiného než název počítače:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

Můžete také nastavit instanci role `APPLICATIONINSIGHTS_ROLE_INSTANCE`cloudu pomocí proměnné prostředí .

## <a name="application-log-capture"></a>Zachycení protokolu aplikací

Application Insights Java 3.0 Preview automaticky zachycuje protokolování aplikací prostřednictvím Log4j, Logback a java.util.logging.

Ve výchozím nastavení zachytí `WARN` veškeré protokolování prováděné na úrovni nebo vyšší úrovni.

Pokud chcete tuto prahovou hodnotu změnit:

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

Jedná se `threshold` o platné hodnoty, `ApplicationInsights.json` které můžete zadat v souboru a jak odpovídají úrovně protokolování v různých architekturách protokolování:

| `threshold`  | Protokol4j  | Přihlašování | JUL     |
|--------------|--------|---------|---------|
| OFF          | OFF    | OFF     | OFF     |
| Fatální        | Fatální  | ERROR   | Závažné  |
| CHYBA/ZÁVAŽNÁ | ERROR  | ERROR   | Závažné  |
| VAROVAT/VAROVAT | Varovat   | Varovat    | WARNING |
| Info         | Info   | Info    | Info    |
| Config       | DEBUG  | DEBUG   | Config  |
| LADĚNÍ/JEMNÉ   | DEBUG  | DEBUG   | Pořádku    |
| Jemnější        | DEBUG  | DEBUG   | Jemnější   |
| TRACE/FINEST | TRACE  | TRACE   | Nejlepší  |
| VŠE          | VŠE    | VŠE     | VŠE     |

## <a name="jmx-metrics"></a>Metriky JMX

Pokud máte nějaké metriky JMX, které máte zájem zachytit:

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

Ve výchozím nastavení, pokud vaše aplikace používá [Micrometer](https://micrometer.io), Application Insights 3.0 (počínaje Náhled.2) se nyní přidá do globálního registru Micrometer a zachytí metriky Mikrometru.

Chcete-li tuto funkci zakázat:

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

Ve výchozím nastavení aplikace Insights Java 3.0 Preview odešle prezenční signál metriky jednou za 15 minut. Pokud používáte metriku prezenčního signálu k aktivaci výstrah, můžete zvýšit frekvenci tohoto prezenčního signálu:

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
> Četnost tohoto prezenčního signálu nelze snížit, protože data prezenčního signálu se také používají ke sledování využití přehledů aplikací.

## <a name="sampling"></a>Vzorkování

Vzorkování je užitečné, pokud potřebujete snížit náklady.
Vzorkování se provádí jako funkce na ID operace (označované také jako ID trasování), takže stejné ID operace bude vždy mít za následek stejné rozhodnutí o odběru vzorků. Tím zajistíte, že nezískáte části distribuované transakce vzorkované v zatímco ostatní části jsou vzorkovány ven.

Pokud například nastavíte vzorkování na 10 %, zobrazí se pouze 10 % transakcí, ale každá z těchto 10 % bude mít úplné podrobnosti o transakcích od začátku do konce.

Zde je příklad, jak nastavit vzorkování na **10 % všech transakcí** – ujistěte se, že jste nastavili vzorkovací frekvenci, která je správná pro váš případ použití:

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

## <a name="http-proxy"></a>HTTP Proxy

Pokud je vaše aplikace za bránou firewall a nemůže se připojit přímo k Application Insights (viz [IP adresy používané Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)), můžete nakonfigurovat Application Insights Java 3.0 Preview tak, aby používala http proxy server:

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

## <a name="self-diagnostics"></a>Vlastní diagnostika

"Vlastní diagnostika" označuje interní protokolování z Application Insights Java 3.0 Preview.

To může být užitečné pro zjitení a diagnostiku problémů se samotnými application insights.

Ve výchozím nastavení se přihlásí `warn`ke konzoli s úrovní , odpovídající této konfiguraci:

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

Platné úrovně `OFF` `ERROR`jsou `WARN` `INFO`, `DEBUG`, `TRACE`, , a .

Pokud se chcete přihlásit k souboru místo přihlášení ke konzoli:

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

Při použití protokolování souborů, `maxSizeMB`jakmile soubor hity , bude převrácení, přičemž jen naposledy vyplněný soubor protokolu kromě aktuálního souboru protokolu.
