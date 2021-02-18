---
title: Pravidla shromažďování dat v Azure Monitor (Preview)
description: Přehled pravidel shromažďování dat (chcete odeslat obecnou) v Azure Monitor včetně jejich obsahu a struktury a způsobu jejich vytváření a práce s nimi.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2021
ms.openlocfilehash: 0bae058804bdb1f199c39497c59e89bda8087424
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100612539"
---
# <a name="data-collection-rules-in-azure-monitor-preview"></a>Pravidla shromažďování dat v Azure Monitor (Preview)
Pravidla shromažďování dat (DCR) definují data přicházející do Azure Monitor a určují, kam se tato data mají odeslat nebo Uložit. Tento článek obsahuje přehled pravidel shromažďování dat, včetně jejich obsahu a struktury a způsobu, jakým můžete s nimi vytvářet a pracovat s nimi.

## <a name="input-sources"></a>Vstupní zdroje
Pravidla shromažďování dat v současné době podporují následující vstupní zdroje:

- Virtuální počítač Azure s agentem Azure Monitor. Viz téma [Konfigurace shromažďování dat pro agenta Azure monitor (Preview)](../agents/data-collection-rule-azure-monitor-agent.md).



## <a name="components-of-a-data-collection-rule"></a>Komponenty pravidla shromažďování dat
Pravidlo shromažďování dat zahrnuje následující součásti.

| Komponenta | Popis |
|:---|:---|
| Zdroje dat | Jedinečný zdroj dat monitorování s vlastním formátem a způsobem, jak vystavit jeho data Mezi příklady zdroje dat patří protokol událostí systému Windows, čítače výkonu a syslog. Každý zdroj dat odpovídá konkrétnímu typu zdroje dat, jak je popsáno níže. |
| Streamy | Jedinečný popisovač, který popisuje sadu zdrojů dat, které budou transformované a schematized jako jeden typ. Každý zdroj dat vyžaduje jeden nebo více datových proudů a jeden datový proud může být použit více zdroji dat. Všechny zdroje dat v datovém proudu sdílejí společné schéma. Použijte několik datových proudů, například pokud chcete odeslat konkrétní zdroj dat do několika tabulek ve stejném pracovním prostoru Log Analytics. |
| Cíle | Sada míst, kde mají být data odeslána Mezi příklady patří Log Analytics pracovní prostor, Azure Monitor metriky a Azure Event Hubs. | 
| Toky dat | Definice, do které se mají odesílat datové proudy, do kterých míst určení | 

Následující diagram znázorňuje komponenty pravidla shromažďování dat a jejich vztahu

[![Diagram DCR](media/data-collection-rule-overview/data-collection-rule-components.png)](media/data-collection-rule-overview/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>Typy zdroje dat
Každý zdroj dat má typ zdroje dat. Každý typ definuje jedinečnou sadu vlastností, které musí být zadány pro každý zdroj dat. Typy zdrojů dat, které jsou aktuálně k dispozici, jsou uvedeny v následující tabulce.

| Typ zdroje dat | Description | 
|:---|:---|
| přípona | Zdroj dat založený na rozšíření virtuálních počítačů |
| Čítače výkonu | Čítače výkonu pro systémy Windows i Linux |
| syslog | Události syslogu na platformě Linux |
| windowsEventLogs | Protokol událostí systému Windows |


## <a name="limits"></a>Omezení
Omezení, která platí pro jednotlivá pravidla shromažďování dat, najdete v tématu [omezení služby Azure monitor](../service-limits.md#data-collection-rules).


## <a name="create-a-dcr"></a>Vytvoření sady DCR
V současné době můžete k vytvoření sady DCR použít kteroukoli z následujících metod:

- [Pomocí Azure Portal](../agents/data-collection-rule-azure-monitor-agent.md) vytvořte pravidlo shromažďování dat a přidružit ho k jednomu nebo více virtuálním počítačům.
- Přímo upravte pravidlo shromažďování dat ve formátu JSON a [odešlete ho pomocí REST API](/rest/api/monitor/datacollectionrules).
- Pomocí [Azure CLI](https://github.com/Azure/azure-cli-extensions/blob/master/src/monitor-control-service/README.md)vytvořte DCR a přidružení.
- Pomocí Azure PowerShell vytvořte DCR a přidružení.
  - [Get-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRule.md)
  - [New-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRule.md)
  - [Set-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Set-AzDataCollectionRule.md)
  - [Update – AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Update-AzDataCollectionRule.md)
  - [Remove-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRule.md)
  - [Get-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRuleAssociation.md)
  - [New-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRuleAssociation.md)
  - [Remove-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRuleAssociation.md)

## <a name="sample-data-collection-rule"></a>Pravidlo shromažďování ukázkových dat
Níže uvedené pravidlo shromažďování ukázkových dat je pro virtuální počítače s agentem pro správu Azure a má následující podrobnosti:

- Data výkonu
  - Shromažďuje konkrétní procesor, paměť, logický disk a čítače fyzického disku každých 15 sekund a nahrává každou minutu.
  - Shromažďuje konkrétní čítače procesů každých 30 sekund a nahrává je každých 5 minut.
- Události systému Windows
  - Shromažďuje události zabezpečení systému Windows a odesílá každou minutu.
  - Shromažďuje události aplikací a událostí systému Windows a odesílá je každých 5 minut.
- Syslog
  - Shromažďuje ladění, kritické a naléhavé události ze zařízení cron.
  - Shromažďuje výstrahy, kritické a naléhavé události ze zařízení syslog.
- Cíle
  - Pošle všechna data do pracovního prostoru Log Analytics s názvem centralWorkspace.

```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "syslogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-Event"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>Další kroky

- [Vytvořte pravidlo shromažďování dat](data-collection-rule-azure-monitor-agent.md) a přidružení k němu z virtuálního počítače pomocí agenta Azure monitor.
