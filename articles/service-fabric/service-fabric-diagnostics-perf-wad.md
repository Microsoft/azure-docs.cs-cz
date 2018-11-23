---
title: Azure Service Fabric – sledování výkonu pomocí rozšíření Windows Azure Diagnostics | Dokumentace Microsoftu
description: Pomocí Windows Azure Diagnostics shromažďovat čítače výkonu pro své clustery Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 0675e06564fcacf5f7d14ef6986762f36df18b1b
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290318"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Monitorování výkonu pomocí rozšíření Windows Azure Diagnostics

Tento dokument popisuje kroky potřebné k nastavení shromažďování čítačů výkonu prostřednictvím rozšíření Windows Azure Diagnostics (WAD) pro clustery Windows. Nastavit pro clustery s Linuxem, [agenta Log Analytics](service-fabric-diagnostics-oms-agent.md) získat čítače výkonu pro uzly. 

 > [!NOTE]
> Rozšíření WAD musí být nasazené na clusteru pro tyto kroky při práci za vás. Pokud není nastavený, přejděte na [agregace událostí a kolekce pomocí Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).  

## <a name="collect-performance-counters-via-the-wadcfg"></a>Shromažďování čítačů výkonu prostřednictvím WadCfg

Získat čítače výkonu pomocí WAD, budete muset upravit konfiguraci odpovídajícím způsobem v šabloně Resource Manageru vašeho clusteru. Postupujte podle těchto kroků přidejte čítače výkonu, které chcete shromáždit do šablony a spusťte upgrade prostředku Resource Manageru.

1. Najít konfiguraci WAD v šabloně vašeho clusteru – vyhledání `WadCfg`. Budete přidávat čítače výkonu shromážděné v souladu `DiagnosticMonitorConfiguration`.

2. Nastavení konfigurace pro shromažďování čítačů výkonu přidejte následující část, která vaše `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod` Definuje konfiguraci frquently hodnoty čítačů, které se shromažďují se přenáší do tabulky Azure storage a do libovolné jímky. 

3. Přidat čítače výkonu chcete shromažďovat pro `PerformanceCounterConfiguration` , který byl deklarován v předchozím kroku. Jednotlivé čítače, které chcete shromažďovat je definována s `counterSpecifier`, `sampleRate`, `unit`, `annotation`a všechny relevantní `sinks`.

Tady je příklad konfigurace s čítačem pro *celkový čas procesoru* (množství času procesoru bylo používáno pro operace zpracování) a *Service Fabric Actor volání metod za sekundu*, jeden Service Fabric vlastní čítače výkonu. Odkazovat na [čítače výkonu služby Reliable Actor](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) a [čítače výkonu služby Reliable Service](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) úplný seznam čítačů výkonů vlastní Service Fabric.

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

 Vzorkovací frekvence čítače lze upravit podle vašich potřeb. Formát pro něj `PT<time><unit>`, takže pokud chcete počítadlo shromažďované každou sekundu, pak byste měli nastavit `"sampleRate": "PT15S"`.

 Můžete také použít proměnné šablony ARM shromažďovat celou řadu čítačů výkonu, které můžou mít po ruce při shromažďování čítačů výkonu jeden proces. V následujícím příkladu, shromažďujeme čas procesoru a systému uvolňování paměti proces a pak 2 čítače výkonu v samotných uzlech pomocí proměnné. 

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

 >[!NOTE]
 >I když můžete použít `*` určit skupiny čítačů výkonu, které jsou pojmenovány podobně, odesílání všechny čítače prostřednictvím jímky (do Application Insights) vyžaduje, že jsou jednotlivě deklarovány. 

4. Po přidání odpovídající čítače, které je potřeba shromáždit, budete muset upgradovat váš prostředek clusteru tak, aby tyto změny se projeví v spuštěný cluster. Uložte upravený `template.json` a otevřete prostředí PowerShell. Upgradem clusteru pomocí `New-AzureRmResourceGroupDeployment`. Volání vyžaduje název skupiny prostředků, soubor aktualizovanou šablonu a soubor parametrů a vyzve správce prostředků na prostředky, které jste provedli odpovídající změny. Po přihlášení k účtu a jsou ve správné předplatné, použijte následující příkaz ke spuštění upgradu:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. Po dokončení upgradu zavádět (přijímá mezi 15 až 45 minut v závislosti na tom, zda je prvním nasazení a velikost vaší skupiny prostředků), WAD by měl být shromažďování čítačů výkonu a jejich odesílání do tabulky s názvem WADPerformanceCountersTable v účtu úložiště spojené s vaším clusterem. Zobrazit čítače výkonu ve službě Application Insights pomocí [přidání jímky AI do šablony Resource Manageru](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Další postup
* Shromažďování dalších čítačů výkonu pro váš cluster. Zobrazit [metriky výkonu](service-fabric-diagnostics-event-generation-perf.md) seznam čítačů, na které se mají shromažďovat.
* [Použití monitorováním a diagnostikou pomocí šablony Azure Resource Manageru a virtuální počítač Windows](../virtual-machines/windows/extensions-diagnostics-template.md) provádět další úpravy vaše `WadCfg`, včetně dalších účtů úložiště k odesílání diagnostických dat konfigurace.
* Přejděte [WadCfg Tvůrce](http://azure.github.io/azure-diagnostics-tools/config-builder/) k vytvoření šablony ze začátku a ujistěte se, že je vaše syntaxe správná.