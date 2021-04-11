---
title: Odeslat data Azure Diagnostics do Application Insights
description: Aktualizujte Azure Diagnostics veřejnou konfiguraci, aby odesílala data do Application Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: b9e9d6b1b5939804b24fd523bf8b7444ed41178f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708590"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Odeslat cloudovou službu, virtuální počítač nebo Service Fabric diagnostická data do Application Insights
Služba Cloud Services, Virtual Machines, Virtual Machine Scale Sets a Service Fabric k shromažďování dat používá rozšíření Azure Diagnostics.  Diagnostika Azure odesílá data do tabulek Azure Storage.  Můžete však také přesměrovat všechny nebo podmnožiny dat do jiných umístění pomocí rozšíření Azure Diagnostics 1,5 nebo novější.

Tento článek popisuje, jak odeslat data z rozšíření Azure Diagnostics do Application Insights.

## <a name="diagnostics-configuration-explained"></a>Vysvětlení konfigurace diagnostiky
Diagnostické rozšíření Azure 1,5 zavedlo jímky, což jsou další místa, kde můžete odesílat diagnostická data.

Příklad konfigurace jímky pro Application Insights:

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- Atribut  *názvu* jímky je řetězcová hodnota, která jednoznačně identifikuje jímku.

- Element **ApplicationInsights** Určuje klíč instrumentace prostředku Application Insights, kde se odesílají data diagnostiky Azure.
    - Pokud nemáte existující prostředek Application Insights, přečtěte si téma [Vytvoření nového prostředku Application Insights](../app/create-new-resource.md) , kde najdete další informace o vytvoření prostředku a získání klíče instrumentace.
    - Pokud vyvíjíte cloudovou službu pomocí sady Azure SDK 2,8 a novější, tento klíč instrumentace se vyplní automaticky. Hodnota je založena na nastavení konfigurace služby **APPINSIGHTS_INSTRUMENTATIONKEY** při vytváření balíčku projektu cloudové služby. Viz [použití Application Insights s Cloud Services](../app/cloudservices.md).

- Element **Channels** obsahuje jeden nebo více prvků **kanálu** .
    - Atribut *Name* jednoznačně odkazuje na daný kanál.
    - Atribut *LogLevel* umožňuje zadat úroveň protokolu, kterou kanál povoluje. Dostupné úrovně protokolu v pořadí od nejvíc do nejnižší informace jsou následující:
        - Verbose
        - Informace
        - Upozornění
        - Chyba
        - Kritické

Kanál funguje jako filtr a umožňuje vybrat konkrétní úrovně protokolu k odeslání do cílové jímky. Můžete například shromáždit podrobné protokoly a odeslat je do úložiště, ale zaslat do jímky pouze chyby.

Tento vztah je znázorněn na následujícím obrázku.

![Veřejná konfigurace diagnostiky](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

Následující obrázek shrnuje konfigurační hodnoty a jejich fungování. Do konfigurace můžete zahrnout více umyvadel na různých úrovních v hierarchii. Jímka na nejvyšší úrovni funguje jako globální nastavení a jedna zadaná v jednotlivém prvku funguje jako přepsání pro toto globální nastavení.

![Konfigurace jímky diagnostiky pomocí Application Insights](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Příklad úplné konfigurace jímky
Tady je kompletní příklad souboru veřejné konfigurace, který
1. pošle všechny chyby do Application Insights (zadané v uzlu **DiagnosticMonitorConfiguration** ).
2. také odesílá protokoly podrobné úrovně pro protokoly aplikací (zadané v uzlu **protokoly** ).

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
V předchozí konfiguraci mají následující řádky následující význam:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Poslat všechna data shromažďovaná diagnostikou Azure

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Odeslání pouze protokolů o chybách do jímky Application Insights

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Odeslat podrobné protokoly aplikací pro Application Insights

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>Omezení

- **Pouze kanály typ protokolu a nikoli čítače výkonu.** Pokud zadáte kanál s prvkem čítače výkonu, bude ignorován.
- **Úroveň protokolu pro kanál nemůže přesáhnout úroveň protokolu pro to, co shromažďuje Diagnostika Azure.** Například nemůžete shromažďovat chyby protokolu aplikace v elementu logs a snažit se odeslat podrobné protokoly do jímky Application Insight. Atribut *scheduledTransferLogLevelFilter* musí vždycky shromažďovat stejné nebo více protokolů než protokoly, které se snažíte odeslat do jímky.
- **Data objektu BLOB shromážděná rozšířením Azure Diagnostics nemůžete odeslat Application Insights.** Například cokoli, co je uvedeno v uzlu *adresáře* . V případě výpisů stavu systému se do úložiště objektů BLOB odesílá skutečný výpis stavu systému a do Application Insights se pošle jenom oznámení, že se vygeneroval výpis stavu systému.

## <a name="next-steps"></a>Další kroky
* Naučte se [zobrazovat informace o diagnostice Azure](../app/cloudservices.md) v Application Insights.
* K povolení rozšíření diagnostiky Azure pro vaši aplikaci použijte [PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) .
* Použití sady [Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) pro povolení rozšíření Azure Diagnostics pro vaši aplikaci

