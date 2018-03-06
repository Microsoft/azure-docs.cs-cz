---
title: "Povolit profileru Application Insights pro aplikace, které jsou hostované na prostředky Azure Cloud Services | Microsoft Docs"
description: "Zjistěte, jak nastavit profileru Application Insights na aplikace běžící v cloudových služeb Azure."
services: application-insights
documentationcenter: 
author: ramach-msft
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: 278d8241ddd67b6df64b7280d4a17c6d3152f223
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-azure-cloud-services"></a>Povolit pro virtuální počítače Azure Service Fabric a cloudové služby Azure Application Insights profileru

Tento článek ukazuje, jak povolit Azure Application Insights profileru na aplikace ASP.NET, který je hostitelem prostředek Azure Cloud Services.

V příkladech v tomto článku zahrnují podporu pro virtuální počítače Azure, sady škálování virtuálního počítače, Azure Service Fabric a cloudových služeb Azure. Příklady spoléhají na šablony, které podporují [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modelu nasazení.  


## <a name="overview"></a>Přehled

Následující obrázek ukazuje, jak profileru Application Insights funguje s aplikacemi, které jsou hostované na prostředky Azure Cloud Services. Azure Cloud Services prostředky zahrnují virtuální počítače, sady škálování, cloudové služby a clusterů Service Fabric. Bitovou kopii virtuálního počítače Azure používá jako příklad.  

  ![Diagram znázorňující, jak profileru Application Insights funguje s prostředky Azure Cloud Services](./media/enable-profiler-compute/overview.png)

Chcete-li plně profileru, musíte změnit konfiguraci ve třech umístěních:

* V podokně instance Application Insights na portálu Azure.
* Zdrojový kód aplikace (například webovou aplikaci ASP.NET).
* Prostředí nasazení definice zdrojový kód (například Azure Resource Manager šablonu v souboru .json).


## <a name="set-up-the-application-insights-instance"></a>Nastavení instance služby Application Insights

1. [Vytvořte nový prostředek Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-create-new-resource), nebo vyberte nějaký existující. 

2. Přejděte do zdroje Application Insights a pak zkopírujte klíč instrumentace.

   ![Umístění klíč instrumentace](./media/enable-profiler-compute/CopyAIKey.png)

3. Chcete-li dokončit nastavení instance služby Application Insights pro profileru, proveďte postup popsané v [povolit profileru](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler).  
    Nemusíte odkaz webové aplikace, protože kroky jsou specifické pro prostředek aplikace služby. Ujistěte se, že je profileru povolené **konfigurace profileru** podokně.


## <a name="set-up-the-application-source-code"></a>Nastavit zdrojovému kódu aplikace

### <a name="aspnet-web-applications-azure-cloud-services-web-roles-or-the-service-fabric-aspnet-web-front-end"></a>Webové aplikace ASP.NET, webové role Azure Cloud Services nebo Service Fabric ASP.NET webového uživatelského rozhraní
Nastavení vaší aplikace odesílat telemetrická data do instance Application Insights na každém `Request` operaci.  

Přidat [Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) na projekt aplikace. Ujistěte se, že verze balíčku NuGet jsou následující:  
  - Pro aplikace ASP.NET: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 nebo novější.
  - Pro aplikace ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 nebo novější.
  - U ostatních aplikací .NET a .NET Core (například bezstavové služby Service Fabric nebo roli pracovního procesu cloudové služby): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) nebo [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 nebo novější.  

### <a name="azure-cloud-services-worker-roles-or-the-service-fabric-stateless-back-end"></a>Role pracovního procesu Azure Cloud Services nebo Service Fabric bezstavové back-end
Kromě dokončení předchozího kroku, pokud je vaše aplikace *není* aplikace ASP.NET nebo ASP.NET Core (např. Pokud role pracovního procesu Azure Cloud Services nebo Service Fabric bezstavové rozhraní API), postupujte takto:  

  1. Časná v životního cyklu aplikací přidejte následující kód:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Další informace o této globální instrumentace klíče konfiguraci najdete v tématu [pomocí Service Fabric pomocí služby Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  2. Pro jakékoliv kód, který chcete instrumentace, přidejte `StartOperation<RequestTelemetry>` **pomocí** příkaz kolem něj, jak je znázorněno v následujícím příkladu:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        Volání metody `StartOperation<RequestTelemetry>` v rámci jiného `StartOperation<RequestTelemetry>` oboru není podporován. Můžete použít `StartOperation<DependencyTelemetry>` v vnořeného obor místo. Příklad:  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```

## <a name="set-up-the-environment-deployment-definition"></a>Nastavit definice nasazení prostředí

Prostředí, ve kterém profileru a spuštění vaší aplikace může být virtuální počítač, škálovací sadu virtuálních počítačů, cluster Service Fabric, nebo cloudových služeb instance.  

### <a name="virtual-machines-scale-sets-or-service-fabric"></a>Virtuální počítače, sady škálování nebo Service Fabric

Úplné příklady najdete v tématu:  
  * [Virtuální počítač](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Škálovací sadu virtuálních počítačů](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Cluster Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Nastavení vašeho prostředí, postupujte takto:
1. K zajištění, že používáte [rozhraní .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nebo novější, je dostačující potvrďte, že je nasazený operační systém `Windows Server 2012 R2` nebo novější.

2. Vyhledejte [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) rozšíření v šabloně nasazení souboru a potom přidejte následující `SinksConfig` části jako podřízeného prvku `WadCfg`. Nahraďte `ApplicationInsightsProfiler` hodnotu vlastnosti s vlastní klíč instrumentace Application Insights:  

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

      Informace o přidání rozšíření diagnostiky do šablony nasazení najdete v tématu [použití monitorování a Diagnostika pomocí šablony virtuálního počítače s Windows a Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


### <a name="azure-cloud-services"></a>Azure Cloud Services

1. K zajištění, že používáte [rozhraní .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nebo novější, je dostačující potvrďte, že *objekt ServiceConfiguration.\*. cscfg* soubory `osFamily` hodnotu "5" nebo novější.

2. Vyhledejte [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* souboru aplikační role, jak je vidět tady:  

   ![Umístění souboru konfigurace diagnostiky](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

   Pokud nemůže najít soubor, zjistěte, jak povolit rozšíření diagnostiky ve vašem projektu Azure Cloud Services najdete v článku [nastavení diagnostiky pro Azure Cloud Services a virtuálních počítačů](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

3. Přidejte následující `SinksConfig` části jako podřízeného prvku `WadCfg`:  

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
> Pokud *diagnostics.wadcfgx* soubor zároveň obsahuje jiné podřízený typu `ApplicationInsights`, všechny tři z následujících klíčů instrumentace musí odpovídat:  
>  * Klíč, který používá vaše aplikace.  
>  * Klíč, který je používán `ApplicationInsights` jímky.  
>  * Klíč, který je používán `ApplicationInsightsProfiler` jímky.  
>
> Hodnota klíče skutečné instrumentace, který je používán můžete najít `ApplicationInsights` jímky *objekt ServiceConfiguration.\*. cscfg* soubory.  
> Po vydání verze Visual Studio 15,5 Azure SDK, pouze klíčů instrumentace, které používají aplikace a `ApplicationInsightsProfiler` potřeba podřízený vzájemně odpovídaly.


## <a name="configure-environment-deployment-and-runtime"></a>Konfigurace nasazení prostředí a prostředí runtime

1. Definice nasazení upravené prostředí nasaďte.  

   Chcete-li použít změny, obvykle zahrnují kompletní šablonou nasazení nebo cloudové služby publikovat prostřednictvím rutin prostředí PowerShell nebo sady Visual Studio.  

   Toto je alternativní postup pro existující virtuální počítače, který dotykem pouze rozšíření Azure Diagnostics:  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

2. Pokud příslušné aplikace běží [IIS](https://www.microsoft.com/web/platform/server.aspx), povolte `IIS Http Tracing` funkce systému Windows pomocí těchto kroků:  

   a. Vytvoření vzdáleného přístupu v prostředí a potom pomocí [funkce Windows přidat]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) okna nebo spusťte následující příkaz v prostředí PowerShell (jako správce):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Pokud navazování vzdáleného přístupu k potížím, můžete použít [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) spusťte následující příkaz:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-profiler-on-on-premises-servers"></a>Povolit profileru na místní servery

Povolení profileru na místním serveru se taky říká spuštěné profileru Statistika aplikace v samostatném režimu. Neváže úprav rozšíření Azure Diagnostics.

Máme žádný plán oficiálně podporovalo profileru pro místní servery. Pokud vás zajímá experimentování se tento scénář, můžete [stáhnout kód podpory](https://github.com/ramach-msft/AIProfiler-Standalone). Snažíme se *není* zodpovědná za údržbu tento kód a reagovat na problémy a žádosti o funkce, které se vztahují ke kódu.

## <a name="next-steps"></a>Další postup

- Generovat provoz vaší aplikace (například spuštění [test dostupnosti](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Potom počkejte 10 až 15 minut pro trasování k odeslání do instance Application Insights.
- V tématu [profileru trasování](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) na portálu Azure.
- Zobrazit nápovědu k řešení potíží s profileru v [profileru řešení potíží s](app-insights-profiler.md#troubleshooting).
- Další informace o profileru v [Application Insights profileru](app-insights-profiler.md).
