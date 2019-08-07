---
title: Profilování živě Cloud Services Azure pomocí Application Insights | Microsoft Docs
description: Povolí Application Insights Profiler pro Azure Cloud Services.
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
ms.openlocfilehash: 93392e379cbb03508fefc1877d5d50e04436b79c
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737215"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profilování živě Cloud Services Azure pomocí Application Insights

Application Insights Profiler můžete nasadit i na tyto služby:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplikace Service Fabric Azure](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler se instaluje s rozšířením Azure Diagnostics. Stačí nakonfigurovat Azure Diagnostics pro instalaci profileru a odesílání profilů do prostředku Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Povolení profileru pro Azure Cloud Services
1. Zkontrolujte, jestli používáte [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nebo novější. Pokud používáte operační systém řady 4, budete muset nainstalovat .NET Framework 4.6.1 nebo novější pomocí [úlohy po spuštění](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-dotnet-install-dotnet). Řada operačních systémů 5 zahrnuje ve výchozím nastavení kompatibilní verzi .NET Framework. 

1. Přidejte [sadu Application Insights SDK do Azure Cloud Services](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

    **Byla opravena chyba v profileru, která se dodává v WAD pro Cloud Services.** Nejnovější verze WAD (1.12.2.0) pro Cloud Services funguje se všemi nejnovějšími verzemi sady App Insights SDK. Hostitelé cloudové služby budou upgradovat WAD automaticky, ale nejsou okamžité. K vynucení upgradu můžete znovu nasadit službu nebo restartovat uzel.

1. Sledovat žádosti pomocí Application Insights:

    * U ASP.NET webových rolí může Application Insights sledovat požadavky automaticky.

    * Pro role pracovního procesu [přidejte kód pro sledování požadavků](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Nakonfigurujte rozšíření Azure Diagnostics pro povolení profileru:

    a. Vyhledejte soubor [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *Diagnostics. wadcfgx* pro vaši aplikační roli, jak je znázorněno zde:  

      ![Umístění konfiguračního souboru diagnostiky](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Pokud soubor nemůžete najít, přečtěte si téma [nastavení diagnostiky pro Azure Cloud Services a Virtual Machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Přidejte následující `SinksConfig` oddíl jako podřízený `WadCfg`prvek:  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    > [!NOTE]
    > Pokud soubor *Diagnostics. wadcfgx* obsahuje také další jímku typu ApplicationInsights, musí se shodovat všechny tři z následujících klíčů instrumentace:  
    > * Klíč, který používá vaše aplikace. 
    > * Klíč, který používá jímka ApplicationInsights. 
    > * Klíč, který používá jímka ApplicationInsightsProfiler. 
    >
    > Můžete najít hodnotu klíče skutečné instrumentace, která používá `ApplicationInsights` jímky *ServiceConfiguration.\*.cscfg* soubory. 
    > Po vydání sady Visual Studio 15,5 Azure SDK se musí navzájem shodovat pouze klíče instrumentace používané aplikací a jímka ApplicationInsightsProfiler.

1. Nasaďte službu s novou konfigurací diagnostiky a Application Insights Profiler je nakonfigurovaná tak, aby běžela ve vaší službě.
 
## <a name="next-steps"></a>Další postup

* Vygenerujte provoz do vaší aplikace (například spusťte [Test dostupnosti](monitor-web-app-availability.md)). Potom počkejte 10 až 15 minut, než se trasování začnou odesílat do instance Application Insights.
* Viz [trasování profileru](profiler-overview.md?toc=/azure/azure-monitor/toc.json) v Azure Portal.
* Řešení potíží s profilerem najdete v tématu [řešení potíží s profilerem](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
