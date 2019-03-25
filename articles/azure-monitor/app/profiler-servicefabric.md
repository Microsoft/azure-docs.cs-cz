---
title: Profilovat živé aplikace Azure Service Fabric pomocí Application Insights | Dokumentace Microsoftu
description: Povolit Profiler pro aplikaci Service Fabric
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 0b1a06d181fc4d2a44d389d47d1f9480c2fdcb40
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401105"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profilovat živé aplikace Azure Service Fabric pomocí Application Insights

Můžete také nasadit Application Insights Profiler za tyto služby:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Nastavte definici nasazení prostředí

Je součástí Azure Diagnostics Application Insights Profiler. S použitím šablony Azure Resource Manageru pro váš cluster Service Fabric můžete nainstalovat rozšíření Azure Diagnostics. Získání [šablonu, která nainstaluje Azure Diagnostics na clusteru Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Nastavení prostředí, pomocí následujících kroků:

1. K zajištění, že používáte [rozhraní .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nebo novější, stačí potvrdit, že je nasazený operační systém `Windows Server 2012 R2` nebo novější.

1. Hledat [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) rozšíření v souboru šablony nasazení.

1. Přidejte následující `SinksConfig` oddílu jako podřízený prvek `WadCfg`. Nahraďte `ApplicationInsightsProfiler` hodnotu vlastnosti s vlastním Instrumentační klíč Application Insights:  

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

1. Nasazení clusteru Service Fabric pomocí šablony Azure Resource Manageru.  
  Pokud je nastavení správné, bude být nainstalované a povolené při instalaci rozšíření Azure Diagnostics Application Insights Profiler. 

1. Přidejte Application Insights do vaší aplikace Service Fabric.  
  Pro Profiler ke shromažďování profily pro své žádosti musí aplikace sledování operací pomocí nástroje Application Insights. Pro rozhraní API je bezstavové, najdete pokyny, jak [sledování požadavků pro profilaci](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Další informace o sledování vlastních operací v jiných typů aplikací, najdete [sledování vlastních operací pomocí Application Insights .NET SDK](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Znovu nasadíte aplikaci.

> [TIP] Pro virtuální počítače, o alternativu k předchozí kroky na základě JSON je pro navigaci na webu Azure Portal k **virtuálních počítačů** > **nastavení diagnostiky** > **Jímky** > **sady posílat diagnostická data do Application Insights na povoleno** a zvolte účet Application Insights nebo konkrétní Instrumentační klíč.

## <a name="next-steps"></a>Další postup

* Generovat provozu do vaší aplikace (Příklad: launch [test dostupnosti](monitor-web-app-availability.md)). Vyčkejte 10 až 15 minut, než se trasování pro odeslání do instance služby Application Insights.
* Zobrazit [trasy Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) na webu Azure Portal.
* Vám pomůžou při řešení potíží Profiler [Profiler řešení potíží s](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
