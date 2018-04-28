---
title: Azure Service Fabric - s příponou Windows Azure Diagnostics pro monitorování výkonu | Microsoft Docs
description: Windows Azure Diagnostics použijte ke shromažďování čítače výkonu pro Azure Service Fabric clusterů.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: a9e8ef7243fcef990dae6ddc6509cd31b3f36e3d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Monitorování výkonu pomocí rozšíření Windows Azure Diagnostics

Tento dokument popisuje kroky potřebné k nastavení kolekce čítače výkonu prostřednictvím rozšíření Windows Azure Diagnostics (WAD) pro Windows clustery. Nastavení pro Linux clusterů [agenta OMS](service-fabric-diagnostics-oms-agent.md) ke shromažďování čítače výkonu pro uzly. 

 > [!NOTE]
> Rozšíření WAD musí být nasazené na clusteru pro tyto kroky při práci za vás. Pokud není nastavena, přejděte na [agregaci událostí a kolekce s použitím Windows Azure Diagnostics](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters).

## <a name="collect-performance-counters-via-the-wadcfg"></a>Shromažďování čítačů výkonu prostřednictvím WadCfg

Shromažďování čítačů výkonu prostřednictvím WAD, budete muset upravit konfiguraci správně v šabloně Resource Manager vašeho clusteru. Postupujte podle těchto kroků přidejte čítače výkonu, které chcete shromažďovat do šablony a spuštění upgradu prostředků Resource Manager.

1. Najít konfiguraci WAD v šabloně váš cluster – najít `WadCfg`. Budete přidávat čítače výkonu v rámci `DiagnosticMonitorConfiguration`.

2. Nastavit konfiguraci ke shromažďování čítačů výkonu přidáním následující části vaší `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod` Definuje, jak frquently hodnoty čítačů, které byly shromážděny přenesou do tabulky úložiště Azure a žádné nakonfigurované jímky. 

3. Přidání čítačů výkonu chcete shromažďovat k `PerformanceCounterConfiguration` , byla deklarována v předchozím kroku. Jednotlivé čítače, které chcete shromažďovat je definovaná pomocí `counterSpecifier`, `sampleRate`, `unit`, `annotation`a všechny relevantní `sinks`.

Tady je příklad konfigurace s čítač pro *celkový čas procesoru* (množství času procesoru bylo používáno pro operace zpracování) a *Service Fabric objektu Actor metoda volání za sekundu*, jeden z čítače výkonu vlastní Service Fabric. Odkazovat na [spolehlivé čítače výkonu objektu Actor](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) a [spolehlivé čítače výkonu služby](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) pro úplný seznam čítače výkonu vlastní Service Fabric.

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

 Vzorkovací frekvence čítače lze upravit podle potřeb. Formát pro něj je `PT<time><unit>`, takže pokud chcete počítadlo shromažďované za sekundu, pak byste měli nastavit `"sampleRate": "PT15S"`.

 >[!NOTE]
 >I když můžete použít `*` určit skupiny čítačů výkonu, které byly pojmenovány podobně, odesílání sledovat nějaké čítače prostřednictvím jímky (Application Insights) vyžaduje, jsou jednotlivě deklarovány. 

4. Po přidání čítačů odpovídající výkonu, které musí být shromažďovány, budete muset upgradovat prostředku clusteru tak, aby tyto změny se projeví v spuštěnému clusteru. Uložit vaše změny `template.json` a otevřete prostředí PowerShell. Můžete upgradovat clusteru pomocí `New-AzureRmResourceGroupDeployment`. Volání vyžaduje název skupiny prostředků, soubor aktualizovanou šablonu a soubor parametrů a vyzve správce prostředků prostředky, které jste aktualizovali provádět odpovídající změny. Jakmile jsou přihlášení k účtu a jsou ve správné předplatné, použijte následující příkaz ke spuštění upgradu:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. Po dokončení upgradu zavedením (trvá mezi 15 – 45 minut), WAD by měl být shromažďování čítačů výkonu a je pošlete na tabulku s názvem WADPerformanceCountersTable v účtu úložiště přidruženého k vašemu clusteru. V tématu čítače výkonu ve službě Application Insights podle [přidávání jímky AI do šablony Resource Manageru](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Další postup
* Shromažďujte další čítače výkonu pro váš cluster. V tématu [metriky výkonu](service-fabric-diagnostics-event-generation-perf.md) seznam čítačů, které mají shromažďovat.
* [Použití monitorování a Diagnostika pomocí šablony virtuálního počítače s Windows a Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) provést další změny vašeho `WadCfg`, včetně konfiguraci účtů úložiště pro odesílat data diagnostiky.
