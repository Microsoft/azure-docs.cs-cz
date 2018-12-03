---
title: Profilování živých cloudových služeb Azure s využitím Application Insights | Dokumentace Microsoftu
description: Povolte Application Insights Profiler pro cloudové služby.
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
ms.openlocfilehash: 5bb70bf56efac28029401b69ee4f87c2738c52e3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721846"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profilování živých cloudových služeb Azure s využitím Application Insights

Můžete také nasadit Application Insights profiler za tyto služby:
* [Azure Web Apps](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplikace Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler se instaluje s rozšíření Windows Azure Diagnostics (WAD). Potřebujete nakonfigurovat WAD instalace profileru a profily posílala vašemu prostředku Application Insights.

## <a name="enable-profiler-for-your-azure-cloud-service"></a>Povolení profileru pro cloudové služby Azure
1. Zkontrolujte, které jste pomocí [rozhraní .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nebo novější.  Stačí potvrdit, že *ServiceConfiguration.\*.cscfg* soubory mají `osFamily` hodnotu "5" nebo novější.
1. Přidat [Application Insights SDK pro cloudovou službu](app-insights-cloudservices.md?toc=/azure/azure-monitor/toc.json).
1. Požadavky na sledování pomocí Application Insights:

    V případě webových rolí technologie ASP.Net Application Insights můžete sledovat, požadavky automaticky.

    V případě rolí pracovních procesů [přidat kód pro sledování požadavků.](app-insights-profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)

    

1. Konfigurace rozšíření Windows Azure Diagnostics (WAD) k povolení profileru.



    1. Vyhledejte [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* souboru pro vaši roli v aplikaci, jak je znázorněno zde:  

       ![Umístění souboru konfigurace diagnostiky](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

        Pokud nemůže najít soubor, přečtěte si, jak povolit diagnostické rozšíření v projektu Azure Cloud Services, najdete v článku [nastavení diagnostiky pro Azure Cloud Services a virtual machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

    1. Přidejte následující `SinksConfig` oddílu jako podřízený prvek `WadCfg`:  

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

    >   **Poznámka:** Pokud *diagnostics.wadcfgx* soubor obsahuje také další jímky typu `ApplicationInsights`, musí odpovídat všechny tři z následujících klíčů instrumentace:  
    >  * Klíč, který používá vaše aplikace.  
    >  * Klíč, který je používán `ApplicationInsights` jímky.  
    >  * Klíč, který je používán `ApplicationInsightsProfiler` jímky.  
    >
    > Můžete najít hodnotu klíče skutečné instrumentace, která používá `ApplicationInsights` jímky *ServiceConfiguration.\*.cscfg* soubory.  
    > Po vydání Visual Studio 15.5 Azure SDK, pouze Instrumentační klíče, které používají aplikace a `ApplicationInsightsProfiler` potřeba jímky vzájemně odpovídaly.
1. Nasazení služby s novou konfiguraci diagnostiky a Application Insights Profiler bude nakonfigurované ke spuštění na vaši službu.
 
## <a name="next-steps"></a>Další postup

- Generovat provozu do vaší aplikace (Příklad: launch [test dostupnosti](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Vyčkejte 10 až 15 minut, než se trasování pro odeslání do instance služby Application Insights.
- Zobrazit [trasy Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) na webu Azure Portal.
- Získejte pomoc při řešení potíží s profileru v [Profiler řešení potíží s](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).