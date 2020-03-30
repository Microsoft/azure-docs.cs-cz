---
title: Odeslání dat diagnostiky Azure do přehledů aplikací
description: Aktualizujte veřejnou konfiguraci Diagnostika Azure a odesílejte data do Application Insights.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: 80d971abd248ca8253a374b488c693ea9aa2ea3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672323"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Odeslání diagnostických dat cloudové služby, virtuálního počítače nebo infrastruktury služeb do přehledů aplikací
Cloudové služby, virtuální počítače, škálovací sady virtuálních počítačů a service fabric používají ke shromažďování dat rozšíření Diagnostika Azure.  Diagnostika Azure odesílá data do tabulek Azure Storage.  Můžete však také kanál všechny nebo podmnožinu dat do jiných umístění pomocí rozšíření Diagnostika Azure 1.5 nebo novější.

Tento článek popisuje, jak odesílat data z rozšíření Diagnostika Azure do Application Insights.

## <a name="diagnostics-configuration-explained"></a>Vysvětlení konfigurace diagnostiky
Rozšíření diagnostiky Azure 1.5 představil jímky, které jsou další umístění, kde můžete odesílat diagnostická data.

Příklad konfigurace jímky pro přehledy aplikací:

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
- *Atribut* **Název jímky** je hodnota řetězce, která jednoznačně identifikuje jímku.

- Prvek **ApplicationInsights** určuje klíč instrumentace prostředku Application insights, kde se posílají data diagnostiky Azure.
    - Pokud nemáte existující prostředek Application Insights, najdete [v tématu Vytvoření nového prostředku Application Insights](../../azure-monitor/app/create-new-resource.md ) další informace o vytvoření prostředku a získání klíče instrumentace.
    - Pokud vyvíjíte cloudovou službu s Azure SDK 2.8 a novější, tento klíč instrumentace se automaticky naplní. Hodnota je založena na nastavení konfigurace **služby APPINSIGHTS_INSTRUMENTATIONKEY** při balení projektu cloudové služby. Viz [Použití přehledů aplikací s cloudovými službami](../../azure-monitor/app/cloudservices.md).

- Channels **Channels** Element obsahuje jeden nebo více prvků **kanálu.**
    - Atribut *name* jednoznačně odkazuje na tento kanál.
    - Atribut *loglevel* umožňuje určit úroveň protokolu, kterou kanál umožňuje. Dostupné úrovně protokolu v pořadí od většiny až nejmenších informací jsou:
        - Verbose
        - Informace
        - Upozornění
        - Chyba
        - Kritická

Kanál se chová jako filtr a umožňuje vybrat konkrétní úrovně protokolu, které chcete odeslat do cílovéjím hodu. Můžete například shromažďovat podrobné protokoly a odesílat je do úložiště, ale odesílat pouze chyby do jímky.

Následující obrázek znázorňuje tento vztah.

![Veřejná konfigurace diagnostiky](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

Následující obrázek shrnuje hodnoty konfigurace a jejich fungování. Můžete zahrnout více jímky v konfiguraci na různých úrovních v hierarchii. Jímka na nejvyšší úrovni funguje jako globální nastavení a nastavení zadané v jednotlivém prvku funguje jako přepsání tohoto globálního nastavení.

![Diagnostika propadá konfiguraci pomocí přehledů aplikací](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Kompletní příklad konfigurace jímky
Zde je úplný příklad veřejného konfiguračního souboru, který
1. odešle všechny chyby do application insights (zadáno v uzlu **DiagnosticMonitorConfiguration)**
2. také odešle protokoly úrovně Verbose pro protokoly aplikací (zadané v uzlu **Protokoly).**

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
V předchozí konfiguraci mají následující řádky následující významy:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Odeslání všech dat, která jsou shromažďována diagnostikou Azure

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Odeslat pouze protokoly chyb do jímky Application Insights

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Odeslání podrobných protokolů aplikací do application insights

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

- **Kanály pouze typ protokolu a nikoli čítače výkonu.** Pokud zadáte kanál s prvkem čítače výkonu, bude ignorován.
- **Úroveň protokolu pro kanál nesmí překročit úroveň protokolu pro co se shromažďuje diagnostikou Azure.** Například nelze shromažďovat chyby protokolu aplikací v logs element a pokusit se odeslat podrobné protokoly do jímky Application Insight. Atribut *scheduledTransferLogLevelFilter* musí vždy shromažďovat stejné nebo více protokolů než protokoly, které se pokoušíte odeslat do jímky.
- **Data objektů blob shromážděná rozšířením diagnostiky Azure nelze odesílat do Application Insights.** Například vše, co je zadáno v uzlu *Adresáře.* Pro výpisy stavu systému je skutečný výpis stavu systému odeslán do úložiště objektů blob a do přehledů aplikací se odesílá pouze oznámení, že byl vygenerován výpis stavu systému.

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak [zobrazit diagnostické informace Azure](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices) v Application Insights.
* Pomocí [PowerShellu](../../cloud-services/cloud-services-diagnostics-powershell.md) povolte rozšíření diagnostiky Azure pro vaši aplikaci.
* Povolení rozšíření diagnostiky Azure pro vaši aplikaci pomocí [Visual Studia](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

