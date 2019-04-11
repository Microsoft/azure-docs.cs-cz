---
title: Profilování živých Azure Cloud Services službou Application Insights | Dokumentace Microsoftu
description: Povolte Application Insights Profiler pro Azure Cloud Services.
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
ms.openlocfilehash: 93d0f148c1fa3f13e79b28e19527251455a1b65c
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470827"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profilování živých Azure Cloud Services službou Application Insights

Můžete také nasadit Application Insights Profiler za tyto služby:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplikace Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler je nainstalován pomocí rozšíření Azure Diagnostics. Stačí ke konfiguraci diagnostiky Azure pro instalaci Profiler a profily posílala vašemu prostředku Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Povolit Profiler pro Azure Cloud Services
1. Ujistěte se, že používáte [rozhraní .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nebo novější. Stačí potvrdit, že *ServiceConfiguration.\*.cscfg* soubory mají `osFamily` hodnotu "5" nebo novější.

1. Přidat [Application Insights SDK pro Azure Cloud Services](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

    **Opravili jsme chybu v profileru, který se dodává v WAD pro cloudové služby.** Nejnovější verze WAD (1.12.2.0) pro Cloud Services funguje s všechny nejnovější verze sady App Insights SDK. Obsluha hostitelů cloudu budou automaticky upgradovat WAD, ale není okamžitý. Vynutit upgrade, můžete znovu nasadit služby nebo restartovat uzel.

1. Požadavky na sledování pomocí Application Insights:

    * V případě webových rolí technologie ASP.NET Application Insights můžete sledovat, požadavky automaticky.

    * V případě rolí pracovních procesů [přidat kód pro sledování požadavků](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Konfigurovat rozšíření Azure Diagnostics pro umožnění Profiler následujícím způsobem:

    a. Vyhledejte [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* souboru pro vaši roli v aplikaci, jak je znázorněno zde:  

      ![Umístění souboru konfigurace diagnostiky](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Pokud nemůže najít soubor, přečtěte si téma [nastavení diagnostiky pro Azure Cloud Services a Virtual Machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Přidejte následující `SinksConfig` oddílu jako podřízený prvek `WadCfg`:  

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
    > Pokud *diagnostics.wadcfgx* soubor obsahuje také další jímky typu ApplicationInsights, musí odpovídat všechny tři z následujících klíčů instrumentace:  
    > * Klíč, který používá vaše aplikace. 
    > * Klíč, který je používán ApplicationInsights jímky. 
    > * Klíč, který je používán ApplicationInsightsProfiler jímky. 
    >
    > Můžete najít hodnotu klíče skutečné instrumentace, která používá `ApplicationInsights` jímky *ServiceConfiguration.\*.cscfg* soubory. 
    > Po vydání Visual Studio 15.5 Azure SDK pouze Instrumentační klíče, které používají aplikace a datovou sadu jímky ApplicationInsightsProfiler muset vzájemně odpovídaly.

1. Nasazení služby s novou konfigurací Diagnostics a Application Insights Profiler je konfigurován pro běh ve službě service.
 
## <a name="next-steps"></a>Další postup

* Generovat provozu do vaší aplikace (Příklad: launch [test dostupnosti](monitor-web-app-availability.md)). Vyčkejte 10 až 15 minut, než se trasování pro odeslání do instance služby Application Insights.
* Zobrazit [trasy Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) na webu Azure Portal.
* Řešení potíží Profiler, najdete v článku [Profiler řešení potíží s](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
