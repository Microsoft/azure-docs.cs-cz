---
title: Profilujte živé aplikace Azure Service Fabric pomocí přehledů aplikací
description: Povolení profileru pro aplikaci Service Fabric
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 664d6eb377185613a1a5670daf6747b482c79d9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671609"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profilovat živé aplikace Azure Service Fabric pomocí přehledů aplikací

Profileru přehledů aplikací můžete také nasadit na tyto služby:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Virtuální počítače Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Nastavení definice nasazení prostředí

Profiler přehledů aplikací je součástí diagnostiky Azure. Rozšíření Diagnostika Azure můžete nainstalovat pomocí šablony Azure Resource Manager pro cluster Service Fabric. Získejte [šablonu, která nainstaluje Diagnostika Azure na clusteru Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Chcete-li nastavit prostředí, provázte následující akce:

1. Profiler podporuje rozhraní .NET Framework a .Net Core. Pokud používáte rozhraní .NET Framework, ujistěte se, že používáte [rozhraní .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nebo novější. Stačí potvrdit, že nasazený operační tým `Windows Server 2012 R2` je nebo novější. Profiler podporuje .NET Core 2.1 a novější aplikace.

1. Vyhledejte rozšíření [Diagnostika Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) v souboru šablony nasazení.

1. Přidejte `SinksConfig` následující oddíl jako `WadCfg`podřízený prvek . Nahraďte hodnotu `ApplicationInsightsProfiler` vlastnosti vlastním klíčem instrumentace Application Insights:  

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

      Informace o přidání rozšíření Diagnostika do šablony nasazení najdete v tématu [Použití monitorování a diagnostiky se šablonami virtuálních zařízení windows a Azure Resource Manager .](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

1. Nasaďte cluster Service Fabric pomocí šablony Azure Resource Manager.  
  Pokud jsou vaše nastavení správná, profiler application insights se nainstaluje a povoluje, když se nainstaluje rozšíření Diagnostika Azure. 

1. Přidejte přehledy aplikací do aplikace Service Fabric.  
  Aby profiler shromažďoval profily pro vaše požadavky, musí vaše aplikace sledovat operace pomocí Application Insights. Pro bezstavová api můžete odkazovat na pokyny pro [sledování požadavků na profilování](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Další informace o sledování vlastních operací v jiných typech aplikací najdete v [tématu Sledování vlastních operací pomocí sady Application Insights .NET SDK](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Znovu nasadit aplikaci.


## <a name="next-steps"></a>Další kroky

* Vygenerujte provoz do aplikace (například spusťte [test dostupnosti).](monitor-web-app-availability.md) Potom počkejte 10 až 15 minut trasování začít odesílat do application insights instance.
* Viz [Trasování profileru](profiler-overview.md?toc=/azure/azure-monitor/toc.json) na webu Azure Portal.
* Nápovědu k řešení problémů profileru naleznete v [tématu Poradce při potížích s profilerem](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
