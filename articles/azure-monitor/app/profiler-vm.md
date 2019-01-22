---
title: Profilovat webové aplikace běžící na Virtuálním počítači Azure s využitím Application Insights Profiler | Dokumentace Microsoftu
description: Profilovat webové aplikace na Virtuálním počítači Azure s využitím Application Insights Profiler.
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
ms.openlocfilehash: 4dfe4140fbc4238af014d838d31aae167cdd1c16
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438758"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Nastavení profilu webové aplikace běžící na virtuálním počítači Azure nebo škálovací sadu virtuálních počítačů s použitím Application Insights Profiler

Můžete také nasadit Azure Application Insights Profiler za tyto služby:
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Profiler nasazení na virtuální počítač nebo škálovací sady virtuálních počítačů
V tomto článku se dozvíte, jak získat Application Insights Profiler běží na vašich virtuálních počítačů (VM) Azure nebo škálovací sady virtuálních počítačů Azure. Profiler je nainstalován pomocí rozšíření Azure Diagnostics pro virtuální počítače. Nakonfigurujte toto rozšíření spustit Profiler a sestavení Application Insights SDK do vaší aplikace.

1. Přidejte Application Insights SDK do vaší [aplikace ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) nebo běžné [aplikace .NET](windows-services.md?toc=/azure/azure-monitor/toc.json).  
  Chcete-li zobrazit profily pro vaše požadavky, musíte poslat telemetrie žádostí Application Insights.

1. Nainstalujte rozšíření Azure Diagnostics na virtuálním počítači. Úplné příklady šablony Resource Manageru najdete v tématu:  
    * [Virtuální počítač](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Škálovací sada virtuálních počítačů](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    Klíčovým faktorem je ApplicationInsightsProfilerSink v WadCfg. Pokud chcete, aby Azure Diagnostics povolit Profiler k odesílání dat do svůj Instrumentační klíč, přidejte do této části jiného jímky.
    
    ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "ApplicationInsightsSink",
            "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
          },
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
          }
        ]
      },
    ```

1. Definice nasazení upravené prostředí nasazení.  

   Použití změny obvykle zahrnuje kompletní šablonu nasazení nebo cloudu založený na službách publikovat prostřednictvím rutin prostředí PowerShell nebo sady Visual Studio.  

   Následující příkazy Powershellu jsou alternativní přístup pro stávající virtuální počítače, které se týkají pouze rozšíření Azure Diagnostics. Přidání výše uvedených ProfilerSink ke konfiguraci, která je vrácena pomocí příkazu Get-AzureRmVMDiagnosticsExtension a předat do příkazu Set-AzureRmVMDiagnosticsExtension aktualizované konfigurace.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Pokud je odpovídající aplikace spuštěna [IIS](https://www.microsoft.com/web/downloads/platform.aspx), povolte `IIS Http Tracing` funkce Windows.

   a. Navázání vzdáleného přístupu do prostředí a pak použít [funkce Windows přidat]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) okna. Nebo spuštěním následujícího příkazu v Powershellu (jako správce):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Pokud navazování vzdáleného přístupu je nějaký problém, můžete použít [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) a spusťte následující příkaz:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Při nasazování aplikace.

## <a name="can-profiler-run-on-on-premises-servers"></a>Můžete spustit Profiler na místní servery?
Máme k dispozici žádný plán pro podporu Application Insights Profiler pro místní servery.

## <a name="next-steps"></a>Další postup

- Generovat provozu do vaší aplikace (Příklad: launch [test dostupnosti](monitor-web-app-availability.md)). Vyčkejte 10 až 15 minut, než se trasování pro odeslání do instance služby Application Insights.
- Zobrazit [trasy Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) na webu Azure Portal.
- Vám pomůžou při řešení potíží Profiler [Profiler řešení potíží s](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
