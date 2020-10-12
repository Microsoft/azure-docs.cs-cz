---
title: Profilování webových aplikací na virtuálním počítači Azure – Application Insights Profiler
description: Profilujte webové aplikace na virtuálním počítači Azure pomocí Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: f514dd7b54ac091535aeab43a8a7d2a645b50a09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87315814"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profilování webových aplikací běžících na virtuálním počítači Azure nebo v sadě škálování virtuálního počítače pomocí Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Application Insights Profiler můžete nasadit i na tyto služby:
* [Azure App Service](./profiler.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](?toc=%2fazure%2fazure-monitor%2ftoc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Nasazení profileru na virtuálním počítači nebo v sadě škálování virtuálního počítače
V tomto článku se dozvíte, jak můžete Application Insights Profiler spustit na virtuálním počítači Azure nebo v sadě škálování virtuálních počítačů Azure. Profiler se instaluje s rozšířením Azure Diagnostics pro virtuální počítače. Nakonfigurujte rozšíření pro spuštění profileru a Sestavte sadu Application Insights SDK do své aplikace.

1. Přidejte sadu Application Insights SDK do [aplikace ASP.NET](./asp-net.md).

   Chcete-li zobrazit profily požadavků, je nutné odeslat telemetrii žádosti do Application Insights.

1. Nainstalujte na virtuální počítač rozšíření Azure Diagnostics. Úplné příklady šablon Správce prostředků naleznete v tématu:  
   * [Virtuální počítač](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Škálovací sada virtuálních počítačů](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     Klíčovou částí je ApplicationInsightsProfilerSink v WadCfg. Pokud chcete Azure Diagnostics povolit profileru odesílat data do vašeho iKeyu, přidejte do této části Další jímku.
    
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

1. Nasaďte upravenou definici nasazení prostředí.  

   Použití úprav obvykle zahrnuje úplné nasazení šablony nebo publikování na základě cloudových služeb prostřednictvím rutin prostředí PowerShell nebo sady Visual Studio.  

   Následující příkazy prostředí PowerShell představují alternativní přístup pro stávající virtuální počítače, které se dotýkají pouze rozšíření Azure Diagnostics. Přidejte výše zmíněné ProfilerSink do konfigurace, kterou vrátí příkaz Get-AzVMDiagnosticsExtension. Pak předejte aktualizovanou konfiguraci do příkazu Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Pokud zamýšlená aplikace běží prostřednictvím [služby IIS](https://www.microsoft.com/web/downloads/platform.aspx), povolte `IIS Http Tracing` funkci Windows.

   a. Navažte vzdálený přístup k prostředí a pak použijte okno [Přidat funkce systému Windows](/iis/configuration/system.webserver/tracing/) . Nebo spusťte následující příkaz v PowerShellu (jako správce):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Pokud je navazování vzdáleného přístupu problém, můžete pomocí rozhraní příkazového [řádku Azure](/cli/azure/get-started-with-azure-cli) spustit následující příkaz:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Nasaďte aplikaci.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Nastavení jímky profileru pomocí Azure Resource Explorer
Ještě nepoužíváme způsob, jak nastavit Application Insights Profiler jímku z portálu. Místo použití prostředí PowerShell, jak je popsáno výše, můžete k nastavení jímky použít Azure Resource Explorer. Pokud však znovu nasadíte virtuální počítač, jímka bude ztracena. Budete muset aktualizovat konfiguraci, kterou použijete při nasazení virtuálního počítače, abyste zachovali toto nastavení.

1. Ověřte, že je nainstalované rozšíření Windows Azure Diagnostics, a to zobrazením rozšíření nainstalovaných pro váš virtuální počítač.  

    ![Ověřte, jestli je nainstalované rozšíření WAD.][wadextension]

2. Najděte pro svůj virtuální počítač rozšíření pro diagnostiku virtuálních počítačů. Přejít na [https://resources.azure.com](https://resources.azure.com) . Rozbalte skupinu prostředků, Microsoft. COMPUTE virtualMachines, název a rozšíření virtuálního počítače.  

    ![V Azure Resource Explorer přejděte na WAD config.][azureresourceexplorer]

3. Přidejte Application Insights Profiler jímka do uzlu SinksConfig pod WadCfg. Pokud ještě nemáte oddíl SinksConfig, možná ho budete muset přidat. Nezapomeňte zadat správné Application Insights iKey v nastavení. V pravém horním rohu budete muset přepnout do režimu Průzkumníka, aby bylo možné číst a zapisovat a stisknout modré tlačítko Upravit.

    ![Přidat Application Insights Profiler jímka][resourceexplorersinksconfig]

4. Až budete hotovi s úpravou konfigurace, stiskněte tlačítko PUT. Pokud je operace Put úspěšná, zobrazí se v prostřední části obrazovky zelená zaškrtávací políčka.

    ![Odeslat požadavek PUT pro uplatnění změn][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Je možné Profiler spustit na místních serverech?
Neplánujeme vám podporovat Application Insights Profiler pro místní servery.

## <a name="next-steps"></a>Další kroky

- Vygenerujte provoz do vaší aplikace (například spusťte [Test dostupnosti](monitor-web-app-availability.md)). Potom počkejte 10 až 15 minut, než se trasování začnou odesílat do instance Application Insights.
- Viz [trasování profileru](profiler-overview.md?toc=/azure/azure-monitor/toc.json) v Azure Portal.
- Nápovědu k řešení potíží s profilerem najdete v tématu [řešení potíží s profilerem](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png

