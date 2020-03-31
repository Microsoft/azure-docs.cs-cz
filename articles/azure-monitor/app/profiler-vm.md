---
title: Profilové webové aplikace na virtuálním počítači Azure – profiler přehledů aplikací
description: Profilujte webové aplikace na virtuálním počítači Azure pomocí Profiler přehledů aplikací.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7c5dfe6ed08df01f78346c76fd5a35e7d64ab520
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671575"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profilujte webové aplikace spuštěné na virtuálním počítači Azure nebo škálovací sadě virtuálních strojů pomocí Nástroje pro profilování aplikací

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Profileru Azure Application Insights můžete také nasadit na tyto služby:
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Nasazení Profileru na virtuálním počítači nebo škálovací sadě virtuálních strojů
Tento článek ukazuje, jak získat Profiler application insights běží na vašem virtuálním počítači (VM) nebo škálovací sady virtuálních strojů Azure. Profiler je nainstalovaný s rozšířením Diagnostika Azure pro virtuální počítače. Nakonfigurujte rozšíření pro spuštění profileru a vytvořte do aplikace sadu Application Insights SDK.

1. Přidejte sady Application Insights SDK do [ASP.NET aplikace](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net).

   Chcete-li zobrazit profily pro vaše požadavky, musíte odeslat telemetrická data požadavku do Application Insights.

1. Nainstalujte rozšíření Azure Diagnostics na virtuální počítač. Úplné příklady šablon Správce prostředků najdete v tématu:  
   * [Virtuální počítač](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Škálovací sada virtuálních počítačů](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     Klíčovou součástí je ApplicationInsightsProfilerSink v WadCfg. Chcete-li, aby diagnostika Azure povoluje profiler k odesílání dat do vašeho iKey, přidejte do této části další jímku.
    
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

1. Nasaďte definici nasazení upraveného prostředí.  

   Použití úprav obvykle zahrnuje úplné nasazení šablony nebo publikování na základě cloudové služby prostřednictvím rutin prostředí PowerShell nebo sady Visual Studio.  

   Následující příkazy Prostředí PowerShell jsou alternativní přístup pro existující virtuální počítače, které se dotýkají pouze rozšíření Diagnostika Azure. Přidejte výše uvedené ProfilerSink do konfigurace, která je vrácena příkazem Get-AzVMDiagnosticsExtension. Potom předaj tecí konfiguraci příkazu Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Pokud je zamýšlená aplikace spuštěna `IIS Http Tracing` prostřednictvím [služby IIS](https://www.microsoft.com/web/downloads/platform.aspx), povolte funkci systému Windows.

   a. Nastolit vzdálený přístup k prostředí a potom použít okno [Přidat funkce systému Windows.]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) Nebo spusťte následující příkaz v PowerShellu (jako správce):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Pokud je problém s navázáním přístupu vzdáleného přístupu, můžete pomocí [příkazového příkazu Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) spustit následující příkaz:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Nasaďte aplikaci.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Nastavení jímky profileru pomocí Průzkumníka prostředků Azure
Ještě nemáme způsob, jak nastavit jímky profileru přehledů aplikací z portálu. Místo použití powershellu, jako je popsáno výše, můžete použít Azure Resource Explorer k nastavení jímky. Ale všimněte si, pokud znovu nasadíte virtuální hod, bude ztracena jímky. Budete muset aktualizovat konfiguraci, kterou používáte při nasazování virtuálního virtuálního provozu, abyste toto nastavení zachovali.

1. Zkontrolujte, zda je nainstalováno rozšíření Diagnostika Windows Azure zobrazením rozšíření nainstalovaných pro váš virtuální počítač.  

    ![Zkontrolujte, zda je nainstalováno rozšíření WAD][wadextension]

2. Najděte rozšíření Diagnostika virtuálních zařízení pro váš virtuální počítač. Přejděte [https://resources.azure.com](https://resources.azure.com)na . Rozbalte skupinu prostředků Microsoft.Compute virtualMachines, název virtuálního počítače a rozšíření.  

    ![Přechod na konfiguraci wadu v Průzkumníku zdrojů Azure][azureresourceexplorer]

3. Přidejte jímko profileru přehledů aplikací do uzlu SinksConfig pod wadcfg. Pokud ještě nemáte sinksConfig oddíl, možná budete muset přidat jeden. Nezapomeňte zadat správné Application Insights iKey ve vašem nastavení. Budete muset přepnout režim průzkumníků na čtení / zápis v pravém horním rohu a stiskněte modré tlačítko "Upravit".

    ![Přidání jímky profileru přehledů aplikací][resourceexplorersinksconfig]

4. Po dokončení úprav konfigurace stiskněte tlačítko "Put". Pokud put je úspěšný, zelená kontrola se zobrazí uprostřed obrazovky.

    ![Odeslat požadavek put pro použití změn][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Může profiler běžet na místních serverech?
Nemáme žádný plán na podporu Profiler Application Insights pro místní servery.

## <a name="next-steps"></a>Další kroky

- Vygenerujte provoz do aplikace (například spusťte [test dostupnosti).](monitor-web-app-availability.md) Potom počkejte 10 až 15 minut trasování začít odesílat do application insights instance.
- Viz [Trasování profileru](profiler-overview.md?toc=/azure/azure-monitor/toc.json) na webu Azure Portal.
- Nápovědu k řešení problémů profileru naleznete v [tématu Poradce při potížích s profilerem](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
