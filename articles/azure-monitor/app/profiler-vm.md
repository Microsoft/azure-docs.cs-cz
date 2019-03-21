---
title: Profilovat webové aplikace běžící na Virtuálním počítači Azure s využitím Application Insights Profiler | Dokumentace Microsoftu
description: Profilovat webové aplikace na Virtuálním počítači Azure s využitím Application Insights Profiler.
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
ms.openlocfilehash: 4cca65e2be44d2c846cd4034f0a9d7e8c7d9af28
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260039"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Nastavení profilu webové aplikace běžící na virtuálním počítači Azure nebo škálovací sadu virtuálních počítačů s použitím Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

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

   Následující příkazy Powershellu jsou alternativním postupu pro stávající virtuální počítače, které přijdou do styku pouze rozšíření Azure Diagnostics. Přidání výše uvedených ProfilerSink ke konfiguraci, která je vrácena pomocí příkazu Get-AzVMDiagnosticsExtension. Předejte do příkazu Set-AzVMDiagnosticsExtension aktualizované konfigurace.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
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

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Nastavení jímky Profiler pomocí Průzkumníka prostředků Azure
Zatím nemáme způsob, jak nastavit jímky Application Insights Profiler z portálu. Místo použití prostředí powershell, jako je popsáno výše, můžete použít Průzkumníka prostředků Azure k nastavení jímky. Ale Poznámka: Pokud nasadíte virtuální počítač znovu jímka budou ztraceny. Budete muset aktualizovat konfiguraci, kterou použijete při nasazování virtuálního počítače zachovat nastavení.

1. Zkontrolujte, jestli je nainstalovaný rozšíření Windows Azure Diagnostics zobrazením rozšíření nainstalované pro virtuální počítač.  

    ![Zkontrolujte, jestli je nainstalované rozšíření WAD][wadextension]

1. Najdete rozšíření diagnostiku virtuálních počítačů pro váš virtuální počítač. Rozbalte vaše skupina prostředků Microsoft.Compute virtualMachines, název virtuálního počítače a rozšíření.  

    ![Přejděte na konfigurace WAD v Průzkumníku prostředků Azure][azureresourceexplorer]

1. Přidáte Application Insights Profiler jímky SinksConfig uzlu WadCfg. Pokud ještě nemáte SinksConfig oddílu, můžete ho přidat. Nezapomeňte zadat správné Instrumentační klíč Application Insights v nastaveních. Budete muset přepnutí průzkumníci režimu pro čtení a zápis v pravém horním rohu a stiskněte tlačítko "Upravit" modrá.

    ![Add Application Insights Profiler Sink][resourceexplorersinksconfig]

1. Po dokončení úprav config, stiskněte "Vložit". Pokud put je úspěšný, zobrazí se zelená značka zaškrtnutí uprostřed obrazovky.

    ![Odeslat žádost, aby se změny][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Můžete spustit Profiler na místní servery?
Máme k dispozici žádný plán pro podporu Application Insights Profiler pro místní servery.

## <a name="next-steps"></a>Další postup

- Generovat provozu do vaší aplikace (Příklad: launch [test dostupnosti](monitor-web-app-availability.md)). Vyčkejte 10 až 15 minut, než se trasování pro odeslání do instance služby Application Insights.
- Zobrazit [trasy Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) na webu Azure Portal.
- Vám pomůžou při řešení potíží Profiler [Profiler řešení potíží s](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
