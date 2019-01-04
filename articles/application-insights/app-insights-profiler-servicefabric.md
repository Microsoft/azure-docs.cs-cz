---
title: Profilovat živé aplikace Azure Service Fabric pomocí Application Insights | Dokumentace Microsoftu
description: Povolení profileru pro aplikace Service Fabric
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 3808b3e93ed7e3ad374054c3c32fd54930f50972
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53606567"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profilovat živé aplikace Azure Service Fabric pomocí Application Insights

Můžete také nasadit Application Insights profiler za tyto služby:
* [Azure App Service](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


## <a name="set-up-the-environment-deployment-definition"></a>Nastavte definici nasazení prostředí

Application Insights Profiler je v ceně s Windows Azure Diagnostics (WAD). Rozšíření WAD můžete nainstalovat pomocí šablony Azure RM pro váš cluster Service Fabric. Je Ukázková šablona: [**Šablona, která nainstaluje WAD clusteru Service Fabric.**](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Nastavení prostředí, pomocí následujících kroků:
1. K zajištění, že používáte [rozhraní .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nebo novější, stačí potvrdit, že je nasazený operační systém `Windows Server 2012 R2` nebo novější.

1. Hledat [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) rozšíření v šablonu nasazení a potom přidejte následující `SinksConfig` oddílu jako podřízený prvek `WadCfg`. Nahraďte `ApplicationInsightsProfiler` hodnotu vlastnosti s vlastním Instrumentační klíč Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Informace o přidání rozšíření diagnostiky do šablony nasazení najdete v tématu [použití monitorováním a diagnostikou pomocí šablony Azure Resource Manageru a virtuální počítač Windows](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
1. Nasazení clusteru Service Fabric pomocí šablony Azure Resource Manageru. Pokud je nastavení správné, bude být nainstalované a povolené při instalaci rozšíření WAD Application Insights Profiler. 
1. Přidejte Application Insights do vaší aplikace Service Fabric. Aplikace musí odesílat do služby application insights profileru začnete shromažďovat profilů pro vaše požadavky, aby data požadavku. Pokyny najdete [tady.](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)
1. Znovu nasadíte aplikaci.

> [TIP] Pro virtuální počítače se o alternativu k výše uvedených kroků formátu json je pro navigaci na webu Azure Portal k **virtuálních počítačů** > **nastavení diagnostiky** >  **Jímky** > Set posílat diagnostická data do Application Insights a **povoleno** a vyberte účet Application Insights nebo konkrétní Instrumentační klíč.

## <a name="next-steps"></a>Další postup

- Generovat provozu do vaší aplikace (Příklad: launch [test dostupnosti](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Vyčkejte 10 až 15 minut, než se trasování pro odeslání do instance služby Application Insights.
- Zobrazit [trasy Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) na webu Azure Portal.
- Získejte pomoc při řešení potíží s profileru v [Profiler řešení potíží s](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).