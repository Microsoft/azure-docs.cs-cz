---
title: Profilovat živé cloudové služby Azure pomocí přehledů aplikací | Dokumenty společnosti Microsoft
description: Povolte Profiler přehledů aplikací pro cloudové služby Azure.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 3fbeb1120e97a884135cd4622a49ef97fd43e58e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671660"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profilovat živé cloudové služby Azure pomocí přehledů aplikací

Profileru přehledů aplikací můžete také nasadit na tyto služby:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplikace Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuální počítače Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Profiler Přehledy aplikací se nainstalovaný s rozšířením Diagnostika Azure. Stačí nakonfigurovat Diagnostika Azure k instalaci Profiler a odeslat profily do vašeho prostředku Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Povolení profileru pro cloudové služby Azure
1. Zkontrolujte, zda používáte [rozhraní .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nebo novější. Pokud používáte os rodiny 4, budete muset nainstalovat rozhraní .NET Framework 4.6.1 nebo novější s [úlohou při spuštění](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-install-dotnet). OS Family 5 ve výchozím nastavení obsahuje kompatibilní verzi rozhraní .NET Framework. 

1. Přidejte do [Cloudových služeb Azure schartizity aplikací](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

    **Chyba v profileru, který je dodáván v WAD pro cloudové služby byla opravena.** Nejnovější verze wadu (1.12.2.0) pro cloudové služby funguje se všemi nejnovějšími verzemi sady App Insights SDK. Hostitelé cloudových služeb upgradují wad automaticky, ale není okamžitý. Chcete-li vynutit upgrade, můžete znovu nasadit službu nebo restartovat uzel.

1. Sledování požadavků pomocí přehledů aplikací:

    * Pro ASP.NET webových rolí může Application Insights sledovat požadavky automaticky.

    * U rolí pracovního procesu [přidejte kód ke sledování požadavků](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Nakonfigurujte rozšíření Diagnostika Azure tak, aby bylo profileru povolováno:

    a. Vyhledejte soubor [Diagnostika Diagnostika](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *Azure.wadcfgx* pro roli aplikace, jak je znázorněno tady:  

      ![Umístění diagnostického konfiguračního souboru](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Pokud soubor nemůžete najít, přečtěte si informace [o nastavení diagnostiky pro Cloudové služby Azure a virtuální počítače](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Přidejte `SinksConfig` následující oddíl jako `WadCfg`podřízený prvek :  

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
    > Pokud soubor *diagnostics.wadcfgx* obsahuje také další jímky typu ApplicationInsights, musí všechny tři následující instrumentace klíče odpovídat:  
    > * Klíč, který používá vaše aplikace. 
    > * Klíč, který se používá jímky ApplicationInsights. 
    > * Klíč, který používá jímka ApplicationInsightsProfiler. 
    >
    > Skutečnou hodnotu klíče instrumentace, kterou `ApplicationInsights` používá jímka, najdete v *konfiguraci\*serviceconfiguration. . cscfg* soubory. 
    > Po vydání sady Visual Studio 15.5 Azure SDK je třeba, aby se vzájemně shodovaly pouze klíče instrumentace, které používá aplikace a jímka ApplicationInsightsProfiler.

1. Nasaďte svou službu pomocí nové konfigurace diagnostiky a profiler application insights je nakonfigurovaný tak, aby se ve vaší službě spouštěl.
 
## <a name="next-steps"></a>Další kroky

* Vygenerujte provoz do aplikace (například spusťte [test dostupnosti).](monitor-web-app-availability.md) Potom počkejte 10 až 15 minut trasování začít odesílat do application insights instance.
* Viz [Trasování profileru](profiler-overview.md?toc=/azure/azure-monitor/toc.json) na webu Azure Portal.
* Potíže s profilerem najdete v [tématu Poradce při potížích s profilerem](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
