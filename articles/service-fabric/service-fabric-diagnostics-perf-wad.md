---
title: Monitorování výkonu pomocí diagnostiky Windows Azure
description: Diagnostika Windows Azure slouží ke shromažďování čítačů výkonu pro clustery Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 0819ca02d088aeb9ada5de1269467f70242bbcca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609906"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Monitorování výkonu s rozšířením Diagnostika Windows Azure

Tento dokument popisuje kroky potřebné k nastavení kolekce čítačů výkonu prostřednictvím rozšíření Diagnostika Windows Azure (WAD) pro clustery Windows. Pro clustery Linux nastavte [agenta Log Analytics](service-fabric-diagnostics-oms-agent.md) pro shromažďování čítačů výkonu pro vaše uzly. 

 > [!NOTE]
> Rozšíření WAD by měla být nasazena v clusteru pro tyto kroky pracovat pro vás. Pokud není nastavena, přemisí na [agregaci událostí a kolekce pomocí Diagnostika Windows Azure](service-fabric-diagnostics-event-aggregation-wad.md).  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Shromažďujte čítače výkonu prostřednictvím modelu WadCfg

Chcete-li shromažďovat čítače výkonu prostřednictvím protokolu WAD, je třeba upravit konfiguraci odpovídajícím způsobem v šabloně Správce prostředků clusteru. Následujícím postupem přidejte čítač výkonu, který chcete shromáždit, do šablony a spusťte upgrade prostředků Správce prostředků.

1. Najděte konfiguraci protokolu WAD v `WadCfg`šabloně clusteru – najděte . Budete přidávat čítače výkonu `DiagnosticMonitorConfiguration`shromažďovat pod .

2. Nastavte konfiguraci tak, aby shromažďovala čítače výkonu, a to přidáním následující části do aplikace `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    Definuje, `scheduledTransferPeriod` jak často se hodnoty čítačů, které se shromažďují, přenesou do tabulky úložiště Azure a do libovolné nakonfigurované jímky. 

3. Přidejte čítače výkonu, které `PerformanceCounterConfiguration` chcete shromáždit, do těch, které byly deklarovány v předchozím kroku. Každý čítač, který chcete `counterSpecifier` `sampleRate`shromáždit, `annotation`je definován `sinks`pomocí , , `unit`, a všechny relevantní .

Zde je příklad konfigurace s čítač pro *celkový čas procesoru* (doba, po kterou byl procesor používán pro operace zpracování) a *vyvolání metody actor fabric service fabric za sekundu*, jeden z čítačů výkonu Service Fabric vlastní. Úplný seznam čítačů vlastních perf service fabric naleznete v části [Čítače výkonu spolehlivého objektu](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) actor a [spolehlivé čítače výkonu služby.](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters)

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

 Vzorkovací frekvenci pro čítač lze upravit podle vašich potřeb. Formát pro něj `PT<time><unit>`je , takže pokud chcete, aby čítač shromažďovány každou sekundu, `"sampleRate": "PT15S"`pak byste měli nastavit .

 Proměnné v šabloně ARM můžete také použít ke shromažďování pole čítačů výkonu, které se mohou hodit při shromažďování čítačů výkonu na proces. V níže uvedeném příkladu shromažďujeme čas procesoru a čas uvolňování paměti na proces a pak 2 čítače výkonu na samotných uzlech, které používají proměnné. 

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

1. Po přidání příslušných čítačů výkonu, které je třeba shromáždit, je třeba upgradovat prostředek clusteru tak, aby se tyto změny projevily ve spuštěném clusteru. Uložte `template.json` upravené prostředí PowerShell. Cluster můžete upgradovat `New-AzResourceGroupDeployment`pomocí aplikace . Volání vyžaduje název skupiny prostředků, aktualizovaný soubor šablony a soubor parametrů a vyzve Správce prostředků k provádění příslušných změn prostředků, které jste aktualizovali. Jakmile jste přihlášeni ke svému účtu a jste ve správném předplatném, spusťte upgrade pomocí následujícího příkazu:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Jakmile upgrade dokončí zavádění (trvá 15–45 minut v závislosti na tom, zda se jedná o první nasazení a velikost vaší skupiny prostředků), měla by protokol WAD shromažďovat čítače výkonu a odesílat je do tabulky s názvem WadPerformanceCountersTable v účtu úložiště přidruženém k vašemu clusteru. Zobrazení čítačů výkonu v Application Insights [přidáním jímky AI do šablony Správce prostředků](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Další kroky
* Shromážděte další čítače výkonu pro váš cluster. V [části Metriky výkonu](service-fabric-diagnostics-event-generation-perf.md) v části seznam čítačů, které byste měli shromažďovat.
* [Pomocí monitorování a diagnostiky se šablonami virtuálních počítačů s Windows a Azure Resource Manageru](../virtual-machines/windows/extensions-diagnostics-template.md) můžete provést další úpravy vašich `WadCfg`, včetně konfigurace dalších účtů úložiště pro odesílání diagnostických dat.
* Navštivte [tvůrce WadCfg](https://azure.github.io/azure-diagnostics-tools/config-builder/) a vytvořte šablonu od začátku a ujistěte se, že vaše syntaxe je správná. (vytvořithttps://azure.github.io/azure-diagnostics-tools/config-builder/) šablonu od nuly a ujistěte se, že vaše syntaxe je správná.
