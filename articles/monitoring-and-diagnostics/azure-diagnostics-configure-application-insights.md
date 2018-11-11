---
title: Konfigurace diagnostiky Azure pro odesílání dat do Application Insights
description: Aktualizujte veřejné konfiguraci diagnostiky Azure pro odesílání dat do Application Insights.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2016
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 2b27b8605e23a4c4333f6f1a9c9dd12444fd43fb
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277116"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Posílat diagnostická data cloudové služby, virtuální počítač nebo Service Fabric do Application Insights
Cloud services, Virtual Machines, Virtual Machine Scale Sets a Service Fabric všechny použití rozšíření diagnostiky Azure shromažďovat data.  Diagnostika Azure odesílá data do tabulky Azure Storage.  Můžete však také všechny kanálu nebo podmnožinu dat do jiných umístění pomocí rozšíření Azure Diagnostics 1.5 nebo novější.

Tento článek popisuje, jak odesílat data z rozšíření Azure Diagnostics do Application Insights.

## <a name="diagnostics-configuration-explained"></a>Vysvětlení konfiguraci diagnostiky
Rozšíření diagnostiky Azure 1.5 zavedené jímky, které jsou další umístění, kde můžete posílat diagnostická data.

Příklad konfigurace jímka pro službu Application Insights:

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
- **Jímky** *název* atribut je řetězcová hodnota, která jednoznačně identifikuje jímky.

- **ApplicationInsights** prvek určuje Instrumentační klíč tohoto prostředku Application insights, kde je odeslána dat diagnostiky Azure.
    - Pokud nemáte existující prostředek Application Insights, přečtěte si téma [vytvořit nový prostředek Application Insights](../application-insights/app-insights-create-new-resource.md) Další informace o vytvoření prostředku a získání Instrumentační klíč.
    - Pokud vyvíjíte Cloudovou službu s Azure SDK 2.8 nebo novější, tento klíč instrumentace se vyplní automaticky. Hodnota je založena na **APPINSIGHTS_INSTRUMENTATIONKEY** nastavení konfigurace služby, při vytváření balíčku projektu cloudové služby. Zobrazit [pomocí Application Insights s cloudovými službami](../application-insights/app-insights-cloudservices.md).

- **Kanály** element obsahuje jeden nebo více **kanál** elementy.
    - *Název* atribut jednoznačně odkazuje na tomto kanálu.
    - *Loglevel* atribut umožňuje určit úroveň protokolu, který umožňuje kanálu. Jsou k dispozici protokol úrovně v pořadí podle nejvíce alespoň informace:
        - Podrobný
        - Informace
        - Upozornění
        - Chyba
        - Kritická

Kanál funguje jako filtr a umožňuje vybrat konkrétní úrovně odeslat do cílového jímky. Například může shromažďovat podrobné protokoly a odeslat je do úložiště ale pouze chyby při odesílání do jímky.

Tento vztah je znázorněný na následujícím obrázku.

![Veřejné konfiguraci diagnostiky](media/azure-diagnostics-configure-application-insights/AzDiag_Channels_App_Insights.png)

Následující obrázek shrnuje hodnoty konfigurace a jak pracují. V konfiguračním souboru v různých úrovních v hierarchii, můžete zahrnout více jímky. Jímka na nejvyšší úrovni funguje jako globální nastavení a je uvedeno na jednotlivý element se chová jako přepsání pro globální nastavení.

![Diagnostika jímky konfigurace pomocí nástroje Application Insights](media/azure-diagnostics-configure-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Příklad konfigurace kompletní jímky
Tady je úplný příklad veřejné konfigurace souboru, který
1. odešle všechny chyby do služby Application Insights (zadané v **DiagnosticMonitorConfiguration** uzel)
2. rovněž odesílá podrobné úrovni protokoly pro protokoly aplikací (zadané v **protokoly** uzlu).

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
Následující řádky v předchozí konfiguraci, mají následující význam:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Odeslat veškerá data, které se shromažďují diagnostiky Azure

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Odeslat pouze protokoly chyb do Application Insights jímky

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Odeslání protokolů s komentářem aplikace Application Insights

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

- **Kanály protokolu pouze typ a není čítače výkonu.** Pokud zadáte kanál s elementem čítače výkonu, je ignorován.
- **Úroveň protokolování pro kanál může mít maximálně úroveň protokolování pro co se shromažďují pomocí diagnostiky Azure.** Například nelze shromažďovat chyby v protokolu aplikace v elementu protokoly a zkuste odeslat podrobné protokoly do jímky Application Insights. *ScheduledTransferLogLevelFilter* atribut musí vždy shromažďovat stejné nebo další protokoly než protokoly se pokoušíte odeslat do jímky.
- **Nelze odeslat data objektů blob shromážděné rozšíření Azure diagnostics do Application Insights.** Například nic zadané v položce *adresáře* uzlu. Pro výpisy skutečné výpisu posílá do úložiště objektů blob a jenom oznámení, že byl vygenerován výpis při selhání se odesílají do Application Insights.

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [zobrazit přehledné informace o Azure diagnostics](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices#view-azure-diagnostic-events) ve službě Application Insights.
* Použití [Powershellu](../cloud-services/cloud-services-diagnostics-powershell.md) povolit rozšíření diagnostiky Azure pro vaši aplikaci.
* Použití [sady Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) povolit rozšíření diagnostiky Azure pro vaši aplikaci
