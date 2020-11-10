---
title: Možnosti konfigurace-Azure Monitor Application Insights Java
description: Možnosti konfigurace pro Azure Monitor Application Insights Java
ms.topic: conceptual
ms.date: 11/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: b703a708af564b9dafc8c1409333a2cfed6d2653
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427696"
---
# <a name="configuration-options-for-azure-monitor-application-insights-java"></a>Možnosti konfigurace pro Azure Monitor Application Insights Java

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

Ve výchozím nastavení Application Insights Java 3,0 očekává, že se konfigurační soubor pojmenuje a že se `applicationinsights.json` nachází ve stejném adresáři jako `applicationinsights-agent-3.0.0.jar` .

Vlastní cestu ke konfiguračnímu souboru můžete zadat buď pomocí

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` Proměnná prostředí nebo
* `applicationinsights.configuration.file` Systémová vlastnost Java

Pokud zadáte relativní cestu, bude vyřešena vzhledem k adresáři, kde `applicationinsights-agent-3.0.0.jar` se nachází.

## <a name="connection-string"></a>Připojovací řetězec

Připojovací řetězec je povinný. Připojovací řetězec najdete v prostředku Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Připojovací řetězec Application Insights":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Připojovací řetězec můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_CONNECTION_STRING` .

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

Název cloudové role můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_ROLE_NAME` .

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

Instanci cloudové role můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_ROLE_INSTANCE` .

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

Procentuální hodnotu vzorkování můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` .

> [!NOTE]
> Pro procento vzorkování vyberte procento, které je blízko 100/N, kde N je celé číslo. V současné době vzorkování nepodporují jiné hodnoty.

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

[//]: # "Poznámka: tady se nedokumentuje APPLICATIONINSIGHTS_JMX_METRICS"
[//]: # "var Embedded ve formátu ENV je v podkladu a měl by se zdokumentovat jenom pro scénář připojení bez kódu."

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

## <a name="telemetry-processors-preview"></a>Procesory telemetrie (Preview)

Tato funkce je ve verzi Preview.

Umožňuje konfigurovat pravidla, která budou použita pro telemetrii požadavků, závislostí a trasování, například:
 * Maskovat citlivá data
 * Podmíněné přidání vlastních dimenzí
 * Aktualizace názvu telemetrie používaného pro agregaci a zobrazení

Další informace najdete v dokumentaci k [procesoru telemetrie](./java-standalone-telemetry-processors.md) .

## <a name="auto-collected-logging"></a>Automaticky shromážděné protokolování

Log4j, Logback a Java. util. protokolování se automaticky instrumentuje a protokolování se provádí pomocí těchto protokolovacích rozhraní, které se automaticky shromáždí.

Ve výchozím nastavení se protokolování shromáždí jenom v případě, že se protokolování provádí na `INFO` úrovni nebo výše.

Pokud chcete změnit tuto úroveň kolekce:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

Prahovou hodnotu můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` .

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
> Frekvence prezenčního signálu se nedá snížit, protože data prezenčního signálu se také používají ke sledování využití Application Insights.

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

[//]: # "Poznamenejte si podporu OpenTelemetry, dokud nepodporujeme 0.10.0, která má obrovské zásadní změny od 0.9.0"

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

`path` může být absolutní nebo relativní cesta. Relativní cesty jsou vyřešeny v adresáři, kde `applicationinsights-agent-3.0.0.jar` je umístěn.

`maxSizeMb` je maximální velikost souboru protokolu před tím, než se vrátí.

`maxHistory` je počet převedených souborů protokolu, které jsou zachovány (kromě aktuálního souboru protokolu).

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
  "httpProxy": {
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