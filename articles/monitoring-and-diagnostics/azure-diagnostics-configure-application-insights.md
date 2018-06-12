---
title: Konfigurace Azure Diagnostics k odesílání dat do služby Application Insights
description: Aktualizace konfigurace veřejného Azure Diagnostics k odesílání dat do služby Application Insights.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2016
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 3e1f4076c7a90cbb348f31b7b92e745fff79a04f
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262133"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Posílat diagnostická data Cloudová služba, virtuální počítač nebo Service Fabric Application insights
Cloudové služby, virtuální počítače sady škálování virtuálního počítače a Service Fabric všechny použít ke shromažďování dat rozšíření Azure Diagnostics.  Azure diagnostics odesílá data do tabulky služby Azure Storage.  Můžete však také všechny kanálu nebo podmnožinu dat do jiných umístění pomocí rozšíření Azure Diagnostics 1.5 nebo novější.

Tento článek popisuje, jak k odesílání dat z rozšíření Azure Diagnostics do Application Insights.

## <a name="diagnostics-configuration-explained"></a>Vysvětlení konfigurace diagnostiky
Rozšíření Azure diagnostiky 1.5 zavedená jímky, které jsou dalších místech, kde můžete poslat diagnostická data.

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
- **Podřízený** *název* atribut je řetězcová hodnota, která jednoznačně identifikuje jímky.

- **ApplicationInsights** element Určuje klíč instrumentace prostředku statistiky aplikace, kde Azure diagnostics data se odesílají.
    - Pokud nemáte existující prostředek Application Insights, přečtěte si téma [vytvořte nový prostředek Application Insights](../application-insights/app-insights-create-new-resource.md) Další informace o vytvoření prostředku a získání klíč instrumentace.
    - Pokud vyvíjíte cloudové služby s Azure SDK 2.8 nebo novější, tento klíč instrumentace se automaticky vyplní. Hodnota je založena na **APPINSIGHTS_INSTRUMENTATIONKEY** nastavení konfigurace služby, když balení projekt cloudové služby. V tématu [Application Insights použít s cloudovými službami](../application-insights/app-insights-cloudservices.md).

- **Kanály** element obsahuje jeden nebo více **kanál** elementy.
    - *Název* atribut jednoznačně odkazuje na tomto kanálu.
    - *Loglevel* atribut umožňuje určit úroveň protokolu, který umožňuje kanál. Jsou k dispozici protokolu úrovně v pořadí podle nejvíc minimálně informace:
        - Podrobný
        - Informace
        - Upozornění
        - Chyba
        - Důležité

Kanál, který funguje jako filtr a umožňuje vybrat konkrétní protokolu úrovně k odeslání do cílové jímky. Například může shromáždit podrobné protokoly a jejich odeslání do úložiště ale odeslat pouze chyby jímky.

Tento vztah je znázorněný na následujícím obrázku.

![Veřejná konfigurace diagnostiky](./media/azure-diagnostics-configure-applicationinsights/AzDiag_Channels_App_Insights.png)

Na následujícím obrázku shrnuje hodnoty konfigurace a funkce. V konfiguraci na různých úrovních v hierarchii můžete zahrnout více jímky. Podřízený na nejvyšší úrovni funguje jako globální nastavení a verze zadaná v jednotlivých element se chová jako přepsání pro tuto globální nastavení.

![Diagnostika jímky konfigurace pomocí nástroje Application Insights](./media/azure-diagnostics-configure-applicationinsights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Příklad konfigurace dokončení jímka
Tady je kompletní příklad veřejné konfigurace souboru, který
1. odešle všechny chyby Application insights (zadané v **DiagnosticMonitorConfiguration** uzlu)
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
Následující řádky v předchozí konfiguraci, mají následující významy:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Odeslat veškerá data, které jsou shromažďována Azure diagnostics

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Pouze protokoly chyb poslat jímky Application Insights

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Odeslání protokolů s podrobné aplikace Application Insights

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

- **Kanály protokolu pouze typ a není výkonu čítače.** Pokud zadáte kanál s elementem čítače výkonu, je ignorován.
- **Úroveň protokolu pro kanál, který nesmí překročit úroveň protokolu pro co shromažďovaných Azure diagnostics.** Například nelze shromažďovat chyby v protokolu aplikace v elementu protokoly a odešlete podrobné protokoly aplikace přehledy jímky. *ScheduledTransferLogLevelFilter* atributu musí vždy shromažďování rovna nebo víc protokolů, než protokoly chcete poslat jímky.
- **Nelze odeslat data objektu blob shromážděné rozšíření Azure diagnostics Application Insights.** Například nic zadané v položce *adresáře* uzlu. Pro výpisů stavu systému je odeslána úložiště objektů blob skutečný stav systému a pouze oznámení, že byl vygenerován stav systému jsou odeslána do Application Insights.

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [zobrazit informace o Azure diagnostics](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices#view-azure-diagnostic-events) ve službě Application Insights.
* Použití [prostředí PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md) povolit rozšíření Azure diagnostics pro vaši aplikaci.
* Použití [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) povolit rozšíření Azure diagnostics pro vaši aplikaci
