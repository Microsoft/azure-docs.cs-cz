---
title: Sledování výkonu pomocí Azure Diagnostics Windows
description: Pomocí Azure Diagnostics Windows Shromážděte čítače výkonu pro clustery Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: c2114f5392da788bb440589e69e704a148731e02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86258620"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Sledování výkonu pomocí rozšíření Windows Azure Diagnostics

Tento dokument popisuje kroky potřebné k nastavení kolekce čítačů výkonu prostřednictvím rozšíření Windows Azure Diagnostics (WAD) pro clustery Windows. U clusterů se systémem Linux nastavte [agenta Log Analytics](service-fabric-diagnostics-oms-agent.md) pro shromažďování čítačů výkonu pro vaše uzly. 

 > [!NOTE]
> Rozšíření WAD by mělo být nasazeno v clusteru, aby bylo možné postupovat za vás. Pokud není nastavená, přejděte do [agregace a kolekce událostí pomocí Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Shromažďování čítačů výkonu prostřednictvím WadCfg

Chcete-li shromažďovat čítače výkonu prostřednictvím WAD, je třeba upravit konfiguraci odpovídajícím způsobem v šabloně Správce prostředků clusteru. Pomocí těchto kroků přidejte do šablony čítač výkonu, který chcete shromáždit, a spusťte upgrade prostředku Správce prostředků.

1. Najděte si konfiguraci WAD v clusteru – vyhledejte šablonu `WadCfg` . Přidáte čítače výkonu, které se budou shromažďovat v rámci `DiagnosticMonitorConfiguration` .

2. Nastavte konfiguraci pro shromažďování čítačů výkonu přidáním následující části do `DiagnosticMonitorConfiguration` . 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod`Definuje, jak často se budou hodnoty shromažďovaných čítačů přenášet do tabulky Azure Storage a do jakékoli nakonfigurované jímky. 

3. Přidejte čítače výkonu, které chcete shromáždit, do rozhraní `PerformanceCounterConfiguration` , které bylo deklarováno v předchozím kroku. Každý čítač, který chcete shromáždit, je definován pomocí `counterSpecifier` , `sampleRate` ,, `unit` `annotation` a všech relevantních `sinks` .

Tady je příklad konfigurace s čítačem pro *Celkový čas procesoru* (čas, kdy se CPU používal pro zpracování operací) a *Service Fabric volání metod objektu actor za sekundu*, což je jeden z Service Fabric vlastních čítačů výkonu. Úplný seznam Service Fabric vlastní čítače výkonu najdete v tématu [čítače výkonu spolehlivého objektu actor](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) a [čítače výkonu spolehlivé služby](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) .

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 Vzorkovací frekvence čítače může být upravena podle vašich potřeb. Formát je `PT<time><unit>` , takže pokud chcete čítače shromažďovat každou sekundu, měli byste nastavit `"sampleRate": "PT15S"` .

 Pomocí proměnných v šabloně ARM můžete také shromažďovat pole čítačů výkonu, které můžou být užitečné při shromažďování čítačů výkonu na proces. V níže uvedeném příkladu shromažďujeme čas procesoru a systém uvolňování paměti pro každý proces a pak 2 čítače výkonu na sebe všech uzlech, které používají proměnné. 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

1. Jakmile přidáte odpovídající čítače výkonu, které je třeba shromáždit, je nutné upgradovat prostředek clusteru tak, aby se tyto změny projevily ve spuštěném clusteru. Uložte upravené `template.json` a otevřené prostředí PowerShell. Cluster můžete upgradovat pomocí `New-AzResourceGroupDeployment` . Volání vyžaduje název skupiny prostředků, aktualizovaného souboru šablony a souboru parametrů a zobrazí výzvu Správce prostředků, aby byly provedeny příslušné změny v prostředcích, které jste aktualizovali. Jakmile se přihlásíte ke svému účtu a jsou ve správném předplatném, spusťte upgrade pomocí následujícího příkazu:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Po dokončení upgradu (trvá od 15-45 minut v závislosti na tom, jestli se jedná o první nasazení a velikost vaší skupiny prostředků), by měl WAD shromažďovat čítače výkonu a odesílat je do tabulky s názvem WADPerformanceCountersTable v účtu úložiště přidruženém k vašemu clusteru. Čítače výkonu můžete zobrazit v Application Insights [přidáním jímky AI do šablony Správce prostředků](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Další kroky
* Shromážděte další čítače výkonu pro váš cluster. Seznam čítačů, které byste měli shromažďovat, najdete v tématu [metrika výkonu](service-fabric-diagnostics-event-generation-perf.md) .
* [Pomocí monitorování a diagnostiky pomocí virtuálních počítačů s Windows a Azure Resource Manager šablon](../virtual-machines/extensions/diagnostics-template.md) můžete provádět další úpravy `WadCfg` , včetně konfigurace dalších účtů úložiště, do kterých se budou posílat diagnostická data.
* Přejděte na [Tvůrce WadCfg](https://azure.github.io/azure-diagnostics-tools/config-builder/) a vytvořte šablonu od začátku a ujistěte se, že je syntaxe správná. ( https://azure.github.io/azure-diagnostics-tools/config-builder/) Chcete-li vytvořit šablonu od začátku a ujistěte se, že je syntaxe správná.
