---
title: Profilovat webové aplikace běžící na Virtuálním počítači Azure s Application Insights Profiler | Dokumentace Microsoftu
description: Profilovat webové aplikace na Virtuálním počítači Azure s Application Insights Profiler.
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
ms.openlocfilehash: e8f80e7d19a961c22b4e1e88556ac165d2558034
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082611"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>Profil webové aplikace běžící na virtuálním počítači Azure nebo škálovací sady virtuálních počítačů pomocí Application Insights Profiler
Můžete také nasadit Application Insights profiler za tyto služby:
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](profiler-cloudservice.md ?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>Nasazení Profiler do virtuálního počítače nebo Škálovací sady
Tato stránka vás provede kroky potřebuje získat Application Insights profiler běží na vašem virtuálním počítači Azure nebo virtuální počítač Azure škálovací sady. Application Insights Profiler se instaluje s rozšíření Windows Azure Diagnostics pro virtuální počítače. Rozšíření je potřeba nakonfigurovat pro spuštění profileru a musí být sestaveny App Insights SDK do vaší aplikace.

1. Přidejte application Insights SDK do vaší [aplikace ASP.Net](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) nebo pravidelných [aplikace .NET.](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json) Musí být pro vaše požadavky na odeslání telemetrie žádostí Application Insights najdete v tématu profily.
1. Nainstalujte rozšíření Windows Azure Diagnostics na virtuálním počítači. Úplné příklady šablony Resource Manageru najdete v tématu:  
    * [Virtuální počítač](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Škálovací sada virtuálních počítačů](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    Klíčovým faktorem je ApplicationInsightsProfilerSink v WadCfg. Přidáte jiný jímky do této části a řekněte WAD chcete povolit profiler k odesílání dat do svůj Instrumentační klíč.
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

   Chcete-li použít změny, obvykle zahrnuje kompletní šablonu nasazení nebo je Cloudová služba založená publikovat prostřednictvím rutin prostředí PowerShell nebo sady Visual Studio.  

   Následující příkazy powershellu jsou alternativní přístup pro stávající virtuální počítače, které se týkají pouze rozšíření Azure Diagnostics. Stačí přidat ProfilerSink, jak bylo uvedeno výše ke konfiguraci, která je vrácena pomocí příkazu Get-AzureRmVMDiagnosticsExtension. Předejte do příkazu Set-AzureRmVMDiagnosticsExcension aktualizované konfigurace.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Pokud je odpovídající aplikace spuštěna [IIS](https://www.microsoft.com/web/downloads/platform.aspx), povolte `IIS Http Tracing` funkce Windows.

   a. Navázání vzdáleného přístupu do prostředí a pak použít [funkce Windows přidat]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) okna, nebo spusťte následující příkaz v Powershellu (jako správce):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Pokud navazování vzdáleného přístupu je nějaký problém, můžete použít [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) a spusťte následující příkaz:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Při nasazování aplikace.

## <a name="can-profiler-run-on-on-premises-servers"></a>Můžete spustit profiler na místní servery?
Nemáme žádné plány pro podporu Application Insights Profiler pro místní servery.

## <a name="next-steps"></a>Další postup

- Generovat provozu do vaší aplikace (Příklad: launch [test dostupnosti](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Vyčkejte 10 až 15 minut, než se trasování pro odeslání do instance služby Application Insights.
- Zobrazit [trasy Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) na webu Azure Portal.
- Získejte pomoc při řešení potíží s profileru v [Profiler řešení potíží s](profiler-troubleshooting.md ?toc=/azure/azure-monitor/toc.json).
