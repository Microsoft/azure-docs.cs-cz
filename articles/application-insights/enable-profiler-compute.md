---
title: Povolit Application Insights Profiler pro aplikace, které jsou hostované na prostředky Azure Cloud Services | Dokumentace Microsoftu
description: Zjistěte, jak nastavit službu Application Insights Profiler na aplikaci běžící v Azure Cloud Services.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/16/2017
ms.reviewer: ramach
ms.author: mbullwin
ms.openlocfilehash: 9eb99ecea8efbbce322e61ac281cd534a112728b
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950662"
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-azure-cloud-services"></a>Povolit Application Insights Profiler pro virtuální počítače Azure, Service Fabric a Azure Cloud Services

Tento článek ukazuje, jak povolit Azure Application Insights Profiler na aplikace ASP.NET, který je hostitelem prostředek Azure Cloud Services.

V příkladech v tomto článku zahrnují podporu pro Azure Virtual Machines, škálovací sady virtuálních počítačů, Azure Service Fabric a Azure Cloud Services. V příkladech Spolehněte se na šablony, které podporují [Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modelu nasazení.  


## <a name="overview"></a>Přehled

Následující obrázek ukazuje, jak se Application Insights Profiler funguje s aplikacemi, které jsou hostované na prostředky Azure Cloud Services. Azure Cloud Services prostředky zahrnují virtuální počítače, škálovací sady, cloud services a clustery Service Fabric. Jako příklad používá image virtuálního počítače Azure.  

  ![Diagram znázorňující, jak služba Application Insights Profiler pracuje s prostředky Azure Cloud Services](./media/enable-profiler-compute/overview.png)

Plně povolit Profiler, musíte změnit konfiguraci na třech místech:

* V podokně instanci Application Insights na webu Azure Portal.
* Zdrojový kód aplikace (například webové aplikace ASP.NET).
* Prostředí nasazení definice zdrojový kód (například šablony Azure Resource Manageru v souboru .json).


## <a name="set-up-the-application-insights-instance"></a>Nastavit instanci Application Insights

1. [Vytvořit nový prostředek Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-create-new-resource), nebo vyberte existující. 

2. Přejděte do prostředku Application Insights a zkopírujte klíč instrumentace.

   ![Umístění Instrumentační klíč](./media/enable-profiler-compute/CopyAIKey.png)

3. Abychom mohli dokončit nastavování instanci Application Insights pro Profiler, dokončete postup, který je popsaný v [Povolit Profiler. Není nutné se propojit webové aplikace, protože kroky jsou specifické pro prostředek aplikace služby. Ujistěte se, že Profiler je povolen v **nakonfigurujte Profiler** podokně.


## <a name="set-up-the-application-source-code"></a>Nastavte si zdrojový kód aplikace

### <a name="aspnet-web-applications-azure-cloud-services-web-roles-or-the-service-fabric-aspnet-web-front-end"></a>Webové aplikace ASP.NET, webové role Azure Cloud Services nebo webového front-endu Service Fabric ASP.NET
Nastavení aplikace odesílat telemetrická data do instance služby Application Insights na každém `Request` operace.  

Přidat [Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) do projektu aplikace. Ujistěte se, že jsou verze balíčku NuGet následujícím způsobem:  
  - Pro aplikace ASP.NET: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 nebo novější.
  - Pro aplikace ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 nebo novější.
  - U ostatních aplikací .NET a .NET Core (například bezstavovou službu Service Fabric nebo role pracovního procesu Cloud Services): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) nebo [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 nebo novější.  

### <a name="azure-cloud-services-worker-roles-or-the-service-fabric-stateless-back-end"></a>Role pracovního procesu Azure Cloud Services nebo Service Fabric bezstavovou back-endu
Kromě dokončení předchozího kroku, pokud je vaše aplikace *není* aplikace ASP.NET nebo ASP.NET Core (například, pokud je role pracovního procesu Azure Cloud Services nebo rozhraní API pro bezstavové aplikace Service Fabric), postupujte takto:  

  1. V rané fázi životního cyklu aplikací přidejte následující kód:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Další informace o této konfiguraci globální Instrumentační klíč najdete v části [pomocí Service Fabric pomocí Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  2. Pro jsou části kódu, který chcete instrumentovat, přidejte `StartOperation<RequestTelemetry>` **použití** příkaz kolem něj, jak je znázorněno v následujícím příkladu:

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

        Volání `StartOperation<RequestTelemetry>` v jiném `StartOperation<RequestTelemetry>` obor se nepodporuje. Můžete použít `StartOperation<DependencyTelemetry>` místo ve vnořeném oboru. Příklad:  
        
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

## <a name="set-up-the-environment-deployment-definition"></a>Nastavte definici nasazení prostředí

Prostředí, ve kterém Profiler a spouštět vaše aplikace můžou být virtuální počítač, škálovací sadu virtuálních počítačů, cluster Service Fabric a cloud services – instance.  

### <a name="virtual-machines-scale-sets-or-service-fabric"></a>Virtuální počítače, škálovací sady nebo Service Fabric

Úplné příklady najdete v tématu:  
  * [Virtuální počítač](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Škálovací sada virtuálních počítačů](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Cluster Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

K nastavení prostředí, postupujte takto:
1. K zajištění, že používáte [rozhraní .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nebo novější, stačí potvrdit, že je nasazený operační systém `Windows Server 2012 R2` nebo novější.

2. Hledat [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) rozšíření v šablonu nasazení a potom přidejte následující `SinksConfig` oddílu jako podřízený prvek `WadCfg`. Nahraďte `ApplicationInsightsProfiler` hodnotu vlastnosti s vlastním Instrumentační klíč Application Insights:  

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

      Informace o přidání rozšíření diagnostiky do šablony nasazení najdete v tématu [použití monitorováním a diagnostikou pomocí šablony Azure Resource Manageru a virtuální počítač Windows](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> Pro virtuální počítače se o alternativu k výše uvedených kroků formátu json je pro navigaci na webu Azure Portal k **virtuálních počítačů** > **nastavení diagnostiky**  >   **Jímky** > Set posílat diagnostická data do Application Insights a **povoleno** a vyberte účet Application Insights nebo konkrétní Instrumentační klíč.

### <a name="azure-cloud-services"></a>Azure Cloud Services

1. K zajištění, že používáte [rozhraní .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nebo novější, stačí potvrdit, že *ServiceConfiguration.\*. cscfg* soubory mají `osFamily` hodnotu "5" nebo novější.

2. Vyhledejte [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* souboru pro vaši roli v aplikaci, jak je znázorněno zde:  

   ![Umístění souboru konfigurace diagnostiky](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

   Pokud nemůže najít soubor, přečtěte si, jak povolit diagnostické rozšíření v projektu Azure Cloud Services, najdete v článku [nastavení diagnostiky pro Azure Cloud Services a virtual machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

3. Přidejte následující `SinksConfig` oddílu jako podřízený prvek `WadCfg`:  

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
> Pokud *diagnostics.wadcfgx* soubor obsahuje také další jímky typu `ApplicationInsights`, musí odpovídat všechny tři z následujících klíčů instrumentace:  
>  * Klíč, který používá vaše aplikace.  
>  * Klíč, který je používán `ApplicationInsights` jímky.  
>  * Klíč, který je používán `ApplicationInsightsProfiler` jímky.  
>
> Můžete najít hodnotu klíče skutečné instrumentace, která používá `ApplicationInsights` jímky *ServiceConfiguration.\*. cscfg* soubory.  
> Po vydání Visual Studio 15.5 Azure SDK, pouze Instrumentační klíče, které používají aplikace a `ApplicationInsightsProfiler` potřeba jímky vzájemně odpovídaly.


## <a name="configure-environment-deployment-and-runtime"></a>Konfigurace prostředí nasazení a modulu runtime

1. Definice nasazení upravené prostředí nasazení.  

   Použití změn, obvykle zahrnuje kompletní šablonu nasazení nebo cloudové služby na základě publikovat prostřednictvím rutin prostředí PowerShell nebo sady Visual Studio.  

   Tady je alternativní přístup pro stávající virtuální počítače, které se týkají pouze rozšíření Azure Diagnostics:  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

2. Pokud je odpovídající aplikace spuštěna [IIS](https://www.microsoft.com/web/downloads/platform.aspx), povolit `IIS Http Tracing` funkce Windows následujícím způsobem:  

   a. Navázání vzdáleného přístupu do prostředí a pak použít [funkce Windows přidat]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) okna, nebo spusťte následující příkaz v Powershellu (jako správce):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Pokud navazování vzdáleného přístupu je nějaký problém, můžete použít [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) a spusťte následující příkaz:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-profiler-on-on-premises-servers"></a>Povolit Profiler na místních serverech

Povoluje se Profiler na místní server se taky říká spuštěné Application Insights Profiler v samostatném režimu. Není vázaný na Úpravy rozšíření Azure Diagnostics.

Máme k dispozici žádný plán oficiálně podporovalo Profiler pro místní servery. Pokud vás zajímají experimentování v tomto scénáři, můžete si [stáhnout kód podpory](https://github.com/ramach-msft/AIProfiler-Standalone). Nepovedlo se *není* zodpovědná za údržbu, že kód, nebo pro reakce na problémy a žádosti o funkce, které se vztahují ke kódu.

## <a name="next-steps"></a>Další postup

- Generovat provozu do vaší aplikace (Příklad: launch [test dostupnosti](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Vyčkejte 10 až 15 minut, než se trasování pro odeslání do instance služby Application Insights.
- Zobrazit [trasy Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) na webu Azure Portal.
- Získejte pomoc při řešení potíží s profileru v [Profiler řešení potíží s](app-insights-profiler.md#troubleshooting).
- Další informace o Profiler v [Application Insights Profiler](app-insights-profiler.md).
