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
ms.date: 03/26/2018
ms.author: srrengar
ms.openlocfilehash: f99206fe673f69c78bf130026207ed58344ccea5
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324421"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Monitorování výkonu pomocí rozšíření Windows Azure Diagnostics

Tento dokument popisuje kroky potřebné k nastavení shromažďování čítačů výkonu prostřednictvím rozšíření Windows Azure Diagnostics (WAD) pro clustery Windows. Nastavit pro clustery s Linuxem, [agenta OMS](service-fabric-diagnostics-oms-agent.md) získat čítače výkonu pro uzly. 

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

 >[!NOTE]
 >I když můžete použít `*` určit skupiny čítačů výkonu, které jsou pojmenovány podobně, odesílání všechny čítače prostřednictvím jímky (do Application Insights) vyžaduje, že jsou jednotlivě deklarovány. 

4. Po přidání odpovídající čítače, které je potřeba shromáždit, budete muset upgradovat váš prostředek clusteru tak, aby tyto změny se projeví v spuštěný cluster. Uložte upravený `template.json` a otevřete prostředí PowerShell. Upgradem clusteru pomocí `New-AzureRmResourceGroupDeployment`. Volání vyžaduje název skupiny prostředků, soubor aktualizovanou šablonu a soubor parametrů a vyzve správce prostředků na prostředky, které jste provedli odpovídající změny. Po přihlášení k účtu a jsou ve správné předplatné, použijte následující příkaz ke spuštění upgradu:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. Po dokončení upgradu zavádět (přijímá mezi 15 až 45 minut), WAD by měly pokračovat ve shromažďování čítačů výkonu a odesílání do tabulky s názvem WADPerformanceCountersTable v účtu úložiště spojené s vaším clusterem. Zobrazit čítače výkonu ve službě Application Insights pomocí [přidání jímky AI do šablony Resource Manageru](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Další postup
* Shromažďování dalších čítačů výkonu pro váš cluster. Zobrazit [metriky výkonu](service-fabric-diagnostics-event-generation-perf.md) seznam čítačů, na které se mají shromažďovat.
* [Použití monitorováním a diagnostikou pomocí šablony Azure Resource Manageru a virtuální počítač Windows](../virtual-machines/windows/extensions-diagnostics-template.md) provádět další úpravy vaše `WadCfg`, včetně dalších účtů úložiště k odesílání diagnostických dat konfigurace.
