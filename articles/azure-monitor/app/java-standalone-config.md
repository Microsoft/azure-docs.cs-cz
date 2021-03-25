---
title: Možnosti konfigurace – Azure Monitor Application Insights pro Java
description: Postup konfigurace Azure Monitor Application Insights pro jazyk Java
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 91ad5a6d95c634300db83d66df8f0407b4544cde
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024162"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>Možnosti konfigurace – Azure Monitor Application Insights pro Java

> [!WARNING]
> **Pokud upgradujete z verze 3,0 Preview**
>
> Přečtěte si pečlivě všechny níže uvedené možnosti konfigurace, protože se úplně změnila struktura JSON, kromě samotného názvu souboru, který všechno byl malý.

## <a name="connection-string-and-role-name"></a>Připojovací řetězec a název role

Mezi nejběžnějším nastavením potřebnými k tomu, aby bylo možné začít, je připojovací řetězec a název role:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

Připojovací řetězec je vyžadován a název role je důležitý vždy, když odesíláte data z různých aplikací do stejného Application Insights prostředku.

Další podrobnosti a další možnosti konfigurace najdete níže.

## <a name="configuration-file-path"></a>Cesta ke konfiguračnímu souboru

Ve výchozím nastavení Application Insights Java 3,0 očekává, že se konfigurační soubor pojmenuje a že se `applicationinsights.json` nachází ve stejném adresáři jako `applicationinsights-agent-3.0.2.jar` .

Vlastní cestu ke konfiguračnímu souboru můžete zadat buď pomocí

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` Proměnná prostředí nebo
* `applicationinsights.configuration.file` Systémová vlastnost Java

Pokud zadáte relativní cestu, bude vyřešena vzhledem k adresáři, kde `applicationinsights-agent-3.0.2.jar` se nachází.

## <a name="connection-string"></a>Připojovací řetězec

Připojovací řetězec je povinný. Připojovací řetězec najdete v prostředku Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Připojovací řetězec Application Insights":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Připojovací řetězec můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_CONNECTION_STRING` (ta pak bude mít přednost, pokud je připojovací řetězec zadán také v konfiguraci JSON).

Při nastavení připojovacího řetězce se agent Java zakáže.

## <a name="cloud-role-name"></a>Název cloudové role

Název role cloudu se používá k označení součásti na mapě aplikace.

Pokud chcete nastavit název cloudové role:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Pokud není název cloudové role nastaven, použije se k označení součásti na mapě aplikace název Application Insights prostředku.

Název cloudové role můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_ROLE_NAME` (ta pak bude mít přednost, pokud je název cloudové role taky zadaný v konfiguraci JSON).

## <a name="cloud-role-instance"></a>Instance cloudové role

Instance role cloudu je standardně nastavená na název počítače.

Pokud chcete nastavit instanci cloudové role na jinou hodnotu než název počítače:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

Instanci cloudové role můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_ROLE_INSTANCE` (ta pak bude mít přednost, pokud je instance cloudové role zadaná taky v konfiguraci JSON).

## <a name="sampling"></a>Vzorkování

Vzorkování je užitečné, pokud potřebujete snížit náklady.
Vzorkování se provádí jako funkce na ID operace (označované také jako ID trasování), takže stejné ID operace bude mít vždycky stejné rozhodnutí o vzorkování. Tím zajistíte, že nebudete mít k disukázce části distribuované transakce v době, kdy ostatní části jsou ukázkové.

Pokud například nastavíte vzorkování na 10%, zobrazí se vám pouze 10% vašich transakcí, ale každá z těchto 10% bude mít kompletní podrobnosti transakce na konci.

Tady je příklad, jak nastavit vzorkování pro zachycení přibližně **1/3 všech transakcí** – Ujistěte se, že jste nastavili vzorkovací frekvenci, která je pro váš případ použití správná:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

Procentuální hodnotu vzorkování můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` (ta pak bude mít přednost, pokud je procento vzorkování zadáno také v konfiguraci JSON).

> [!NOTE]
> Pro procento vzorkování vyberte procento, které je blízko 100/N, kde N je celé číslo. V současné době vzorkování nepodporují jiné hodnoty.

## <a name="sampling-overrides-preview"></a>Přepsání vzorkování (Preview)

Tato funkce je ve verzi Preview, počínaje 3.0.3-BETA. 2.

Přepsání vzorkování umožňují přepsat [výchozí procento vzorkování](#sampling), například:
* Nastavte procento vzorkování na 0 (nebo malou hodnotu) pro kontroly stavu s vysokou úrovní šumu.
* Nastavte procento vzorkování na 0 (nebo malou hodnotu) pro volání závislostí s vysokou úrovní šumu.
* Nastavte procento vzorkování na 100 pro důležitý typ žádosti (např.), přestože `/login` máte výchozí vzorkování nakonfigurované na něco menšího.

Další informace najdete v dokumentaci k [přepsání vzorkování](./java-standalone-sampling-overrides.md) .

## <a name="jmx-metrics"></a>JMX metriky

Pokud chcete shromáždit některé další JMX metriky:

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` je název metriky, který se přiřadí této JMX metriky (může být cokoli).

`objectName` je [název objektu](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) JMX MBean, který chcete shromáždit.

`attribute` je název atributu uvnitř JMX MBean, který chcete shromáždit.

Hodnoty metriky numeric a Boolean JMX jsou podporované. Logické JMX metriky jsou namapovány na `0` hodnotu false a `1` na hodnotu true.

## <a name="custom-dimensions"></a>Vlastní rozměry

Pokud chcete přidat vlastní dimenze do všech telemetrie:

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` dá se použít ke čtení hodnoty ze zadané proměnné prostředí při spuštění.

> [!NOTE]
> Od verze 3.0.2, pokud přidáte vlastní dimenzi s názvem `service.version` , bude hodnota uložena ve `application_Version` sloupci v tabulce protokoly Application Insights místo jako vlastní dimenze.

## <a name="telemetry-processors-preview"></a>Procesory telemetrie (Preview)

Tato funkce je ve verzi Preview.

Umožňuje konfigurovat pravidla, která budou použita pro telemetrii požadavků, závislostí a trasování, například:
 * Maskovat citlivá data
 * Podmíněné přidání vlastních dimenzí
 * Aktualizujte název rozsahu, který se používá k agregaci podobné telemetrie v Azure Portal.
 * Přetažením konkrétních atributů span můžete řídit náklady na ingestování.

Další informace najdete v dokumentaci k [procesoru telemetrie](./java-standalone-telemetry-processors.md) .

> [!NOTE]
> Pokud chcete vynechat konkrétní (celé) rozsahy pro řízení nákladů na ingestování, přečtěte si téma [přepsání vzorkování](./java-standalone-sampling-overrides.md).

## <a name="auto-collected-logging"></a>Automaticky shromážděné protokolování

Log4j, Logback a Java. util. protokolování se automaticky instrumentuje a protokolování se provádí pomocí těchto protokolovacích rozhraní, které se automaticky shromáždí.

Protokolování je zachyceno pouze v případě, že nejprve splňuje úroveň nakonfigurovanou pro protokolovací rozhraní a druhá také splňuje úroveň nakonfigurovanou pro Application Insights.

Pokud je například vaše protokolovací rozhraní nakonfigurované na protokolovat `WARN` (a vyšší) z balíčku `com.example` a Application Insights je nakonfigurované na zachycení `INFO` (a vyšší), Application Insights bude zachytit `WARN` (a výše) z balíčku `com.example` .

Výchozí úroveň konfigurovaná pro Application Insights je `INFO` . Pokud chcete změnit tuto úroveň:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

Úroveň můžete nastavit také pomocí proměnné prostředí `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` (která pak bude mít přednost, pokud je úroveň zadána také v konfiguraci JSON).

Jedná se o platné `level` hodnoty, které můžete zadat v `applicationinsights.json` souboru, a způsob, jakým odpovídají úrovně protokolování v různých protokolovacích rozhraních:

| úroveň             | Log4j  | Logback | JUL     |
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

> [!NOTE]
> Pokud je předán objekt výjimky do protokolovacího nástroje, pak se zpráva protokolu (a podrobnosti objektu výjimky) zobrazí v Azure Portal v `exceptions` tabulce místo v `traces` tabulce.

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Automaticky shromážděná metrika mikroměřiče (včetně metriky pohánějícího spouštěcího zařízení)

Pokud vaše aplikace používá [mikroměřiče](https://micrometer.io), pak se automaticky shromažďují metriky, které jsou odesílány do globálního registru mikroměřiče.

Pokud vaše aplikace používá [pružinový spouštěcí systém](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html), budou se také automaticky shromažďovat metriky nakonfigurované pomocí pružinového spouštěcího válce.

Zakázání automatické kolekce metrik mikroměřičů (včetně metriky pohánějícího spouštěcích procesorů):

> [!NOTE]
> Vlastní metriky se účtují samostatně a můžou vygenerovat další náklady. Ujistěte se, že jste zkontrolovali podrobné [informace o cenách](https://azure.microsoft.com/pricing/details/monitor/). Pokud chcete zakázat metriky mikroměřičů a pružinového poháněcí, přidejte do konfiguračního souboru níže uvedenou konfiguraci.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="suppressing-specific-auto-collected-telemetry"></a>Potlačení konkrétní automaticky shromážděné telemetrie

Počínaje verzí 3.0.2 se konkrétní automaticky shromážděná telemetrie dá potlačit pomocí těchto možností konfigurace:

```json
{
  "instrumentation": {
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "kafka": {
      "enabled": false
    },
    "micrometer": {
      "enabled": false
    },
    "mongo": {
      "enabled": false
    },
    "redis": {
      "enabled": false
    }
  }
}
```

## <a name="heartbeat"></a>Tep

Ve výchozím nastavení Application Insights Java 3,0 pošle metriku prezenčního signálu každých 15 minut. Pokud používáte metriku prezenčního signálu k aktivaci výstrah, můžete zvýšit frekvenci tohoto prezenčního signálu:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> Interval nelze prodloužit na více než 15 minut, protože data prezenčního signálu slouží také ke sledování využití Application Insights.

## <a name="http-proxy"></a>Proxy server HTTP

Pokud je vaše aplikace za bránou firewall a nemůže se připojit přímo k Application Insights (viz [IP adresy, které používá Application Insights](./ip-addresses.md)), můžete nakonfigurovat Application Insights Java 3,0 k používání proxy serveru http:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights Java 3,0 také respektuje globální `-Dhttps.proxyHost` a `-Dhttps.proxyPort` Pokud jsou nastavené.

## <a name="metric-interval"></a>Interval metriky

Tato funkce je ve verzi Preview.

Ve výchozím nastavení jsou metriky zachyceny každých 60 sekund.

Od verze 3.0.3-BETA můžete změnit tento interval:

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

Nastavení platí pro všechny tyto metriky:

* Výchozí čítače výkonu, např. procesor a paměť
* Výchozí vlastní metriky, např. časování shromažďování paměti
* Nakonfigurované metriky JMX ([viz výše](#jmx-metrics))
* Metriky mikroměřiče ([viz výše](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics))


[//]: # "Poznámka: podpora OpenTelemetry je v privátní verzi Preview, dokud rozhraní OpenTelemetry API nedosáhne 1,0."

[//]: # "Podpora # # pro verze OpenTelemetry API starší než 1,0"

[//]: # "Podpora pro verze OpenTelemetry API v předběžných 1,0 je výslovný souhlas, protože rozhraní OpenTelemetry API ještě není stabilní."
[//]: # "takže každá verze agenta podporuje jenom konkrétní verze OpenTelemetry API, které jsou starší než 1,0."
[//]: # "(Toto omezení se nepoužije po vydání rozhraní OpenTelemetry API 1,0)."

[//]: # "formát JSON"
[//]: # "{"
[//]: # "  \"Náhled \" : {"
[//]: # "    \"openTelemetryApiSupport \" : true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>Samoobslužná Diagnostika

"Samoobslužná Diagnostika" odkazuje na interní protokolování z Application Insights Java 3,0.

Tato funkce může být užitečná pro hledání a diagnostikování problémů Application Insights sebe sama.

Ve výchozím nastavení Application Insights Java 3,0 protokoluje na úrovni `INFO` souboru `applicationinsights.log` i konzole, které odpovídají této konfiguraci:

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` může to být jedna z `file` , `console` nebo `file+console` .

`level` může to být jedna z `OFF` ,,,, `ERROR` `WARN` `INFO` `DEBUG` nebo `TRACE` .

`path` může být absolutní nebo relativní cesta. Relativní cesty jsou vyřešeny v adresáři, kde `applicationinsights-agent-3.0.2.jar` je umístěn.

`maxSizeMb` je maximální velikost souboru protokolu před tím, než se vrátí.

`maxHistory` je počet převedených souborů protokolu, které jsou zachovány (kromě aktuálního souboru protokolu).

Počínaje verzí 3.0.2 můžete také nastavit samoobslužnou diagnostiku `level` pomocí proměnné prostředí `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` (která pak bude mít přednost, pokud `level` je v konfiguraci JSON také zadaná možnost samoobslužná Diagnostika).

## <a name="an-example"></a>Příklad

Toto je pouze příklad, který ukazuje, jak konfigurační soubor vypadá s více komponentami.
Nakonfigurujte prosím konkrétní možnosti podle svých potřeb.

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "proxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```
