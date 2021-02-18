---
title: Profilování živých aplikací Service Fabric Azure pomocí Application Insights
description: Povolení profileru pro aplikaci Service Fabric
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 67e7765a1f46c2be5790c11687e06ea624702b9b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589566"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profilování živých aplikací Service Fabric Azure pomocí Application Insights

Application Insights Profiler můžete nasadit i na tyto služby:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Nastavení definice nasazení prostředí

Application Insights Profiler je součástí Azure Diagnostics. Azure Diagnostics rozšíření můžete nainstalovat pomocí Azure Resource Manager šablony pro Service Fabric clusteru. Získejte [šablonu, která nainstaluje Azure Diagnostics v clusteru Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Chcete-li nastavit prostředí, proveďte následující akce:

1. Profiler podporuje .NET Framework a .Net Core. Pokud používáte .NET Framework, ujistěte se, že používáte [.NET Framework 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nebo novější. Je dostačující ověřit, zda je nasazený operační systém `Windows Server 2012 R2` nebo novější. Profiler podporuje .NET Core 2,1 a novější aplikace.

1. Vyhledejte rozšíření [Azure Diagnostics](../agents/diagnostics-extension-overview.md) v souboru šablony nasazení.

1. Přidejte následující `SinksConfig` oddíl jako podřízený element prvku `WadCfg` . Nahraďte `ApplicationInsightsProfiler` hodnotu vlastnosti vlastním klíčem instrumentace Application Insights:  

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

      Informace o přidání rozšíření diagnostiky do šablony nasazení najdete v tématu [použití monitorování a diagnostiky pomocí virtuálních počítačů s Windows a Azure Resource Manager šablon](../../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json).

1. Nasaďte Service Fabric cluster pomocí šablony Azure Resource Manager.  
  Pokud je vaše nastavení správné, Application Insights Profiler se nainstaluje a povolí při instalaci rozšíření Azure Diagnostics. 

1. Přidejte Application Insights do aplikace Service Fabric.  
  Aby Profiler mohl shromažďovat profily pro vaše požadavky, musí vaše aplikace sledovat operace s Application Insights. Pro Bezstavová rozhraní API můžete použít pokyny pro [Sledování žádostí o profilování](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Další informace o sledování vlastních operací v jiných typech aplikací najdete v tématu [sledování vlastních operací pomocí Application Insights .NET SDK](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Znovu nasaďte aplikaci.


## <a name="next-steps"></a>Další kroky

* Vygenerujte provoz do vaší aplikace (například spusťte [Test dostupnosti](monitor-web-app-availability.md)). Potom počkejte 10 až 15 minut, než se trasování začnou odesílat do instance Application Insights.
* Viz [trasování profileru](profiler-overview.md?toc=/azure/azure-monitor/toc.json) v Azure Portal.
* Nápovědu k řešení potíží s profilerem najdete v tématu [řešení potíží s profilerem](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
