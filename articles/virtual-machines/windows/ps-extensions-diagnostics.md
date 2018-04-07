---
title: Povolte diagnostiku na virtuální počítač s Windows pomocí prostředí Azure PowerShell | Microsoft Docs
services: virtual-machines-windows
documentationcenter: ''
description: Další informace o použití prostředí PowerShell pro povolení Azure Diagnostics v virtuálního počítače se systémem Windows
author: sbtron
manager: jeconnoc
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: saurabh
ms.openlocfilehash: 49c6fe69afdee6a075d9f888f8a2417455bc7e51
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Použití PowerShellu k povolení Diagnostiky Azure na virtuálním počítači s Windows
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Azure Diagnostics je funkce v rámci Azure, která umožňuje shromažďování diagnostických dat na nasazené aplikace. Rozšíření diagnostiky můžete použít ke shromažďování diagnostických dat, jako jsou protokoly aplikací a čítače výkonu z Azure virtuálního počítače (VM) se systémem Windows. Tento článek popisuje, jak pomocí prostředí Windows PowerShell povolit rozšíření diagnostiky pro virtuální počítač. V tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) pro součásti potřebné k tomuto článku.

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Povolit rozšíření diagnostiky, pokud používáte model nasazení Resource Manager
Při vytváření virtuální počítač s Windows pomocí modelu nasazení Azure Resource Manager přidáním konfigurace rozšíření do šablony Resource Manageru můžete povolit rozšíření diagnostiky. V tématu [vytvoření virtuálního počítače s Windows pomocí monitorování a Diagnostika pomocí šablony Azure Resource Manager](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Pokud chcete povolit rozšíření diagnostiky na existující virtuální počítač, který byl vytvořen pomocí modelu nasazení Resource Manager, můžete použít [Set-AzureRMVMDiagnosticsExtension](/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension) rutiny prostředí PowerShell, jak je uvedeno níže.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* je cesta k souboru, který obsahuje konfiguraci diagnostiky v XML, jak je popsáno v [ukázka](#sample-diagnostics-configuration) níže.  

Pokud konfigurační soubor Diagnostika Určuje **StorageAccount** element s názvem účtu úložiště, pak se *Set-AzureRMVMDiagnosticsExtension* skript automaticky nastaví rozšíření diagnostiky k odesílání diagnostických dat k tomuto účtu úložiště. Tento postup vyžaduje účet úložiště musí být ve stejném předplatném jako virtuální počítač.

Pokud žádné **StorageAccount** zadaná v konfiguraci diagnostiky, je třeba předat *StorageAccountName* do rutiny parametr. Pokud *StorageAccountName* je zadán parametr a potom rutinu bude vždy používat účet úložiště, který je zadaný v parametru a není ten, který je uveden v konfiguračním souboru diagnostiky.

Pokud se účet úložiště diagnostiky v jiném předplatném. z virtuálního počítače, pak je potřeba explicitně předat *StorageAccountName* a *StorageAccountKey* parametry rutiny. *StorageAccountKey* parametr není nutný, pokud je účet úložiště diagnostiky ve stejném předplatném jako rutiny můžete automaticky vyhledat a nastavit hodnotu klíče při povolování rozšíření diagnostiky. Pokud je účet úložiště diagnostiky v jiném předplatném, potom rutinu nemusí být možné získat klíč automaticky a je potřeba explicitně zadat klíč prostřednictvím *StorageAccountKey* parametr.  

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Jakmile bude rozšíření diagnostiky je povoleno na virtuálním počítači, můžete získat aktuální nastavení pomocí [Get-AzureRMVmDiagnosticsExtension](/powershell/module/azurerm.compute/get-azurermvmdiagnosticsextension) rutiny.

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

Vrátí rutina *PublicSettings*, který obsahuje konfiguraci diagnostiky. Existují dva typy konfigurace podporována, WadCfg a xmlCfg. WadCfg je konfigurace JSON a xmlCfg je konfigurační soubor XML ve formátu kódováním Base64. Ke čtení souboru XML, budete muset dekódovat.

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

[Odebrat AzureRMVmDiagnosticsExtension](/powershell/module/azurerm.compute/remove-azurermvmdiagnosticsextension) rutiny lze odebrat rozšíření diagnostiky z virtuálního počítače.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Povolit rozšíření diagnostiky, pokud používáte model nasazení classic
Můžete použít [Set-AzureVMDiagnosticsExtension](/powershell/module/azure/set-azurevmdiagnosticsextension) rutiny povolit rozšíření diagnostiky pro virtuální počítač, který vytvoříte pomocí modelu nasazení classic. Následující příklad ukazuje postup vytvoření nového virtuálního počítače pomocí modelu nasazení classic se povolit rozšíření diagnostiky.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Pokud chcete povolit rozšíření diagnostiky na existující virtuální počítač, který byl vytvořen pomocí modelu nasazení classic, nejprve použijte [Get-AzureVM](/powershell/module/azure/get-azurevm) rutiny konfigurace virtuálního počítače. Aktualizujte konfiguraci virtuálního počítače do zahrnout rozšíření diagnostiky pomocí [Set-AzureVMDiagnosticsExtension](/powershell/module/azure/set-azurevmdiagnosticsextension) rutiny. Navíc platí aktualizovanou konfiguraci pro virtuální počítač pomocí [aktualizace-AzureVM](/powershell/module/azure/update-azurevm).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Ukázková konfigurace diagnostiky
Následující kód XML lze použít pro veřejné konfigurace diagnostiky pomocí výše uvedených skriptů. Tato ukázková konfigurace se k účtu úložiště diagnostiky, spolu s chybami aplikací, zabezpečení a systému kanály v protokolech událostí systému Windows a všechny chyby z protokolů diagnostiky infrastruktury přenosu různých čítače výkonu.

Konfigurace se musí aktualizovat, aby zahrnují následující:

* *ResourceID* atribut **metriky** element je třeba aktualizovat s ID prostředku pro virtuální počítač.
  
  * ID prostředku lze sestavit pomocí vzoru následující: "/ subscriptions / {*ID odběru pro odběr s virtuálním Počítačem*} /resourceGroups/ {*resourcegroup název pro virtuální počítač*} / providers/Microsoft.Compute/virtualMachines/ {*název virtuálního počítače*}".
  * Například pokud ID odběru pro předplatné, ve kterém je spuštěný virtuální počítač je **11111111-1111-1111-1111-111111111111**, je název skupiny prostředků pro skupinu prostředků **MyResourceGroup**, a název virtuálního počítače je **MyWindowsVM**, pak hodnota *resourceID* by:
    
      ```
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Další informace o tom, jsou generovány metriky na základě konfigurace čítače a metriky výkonu najdete v tématu [tabulky Azure Diagnostics metriky v úložišti](extensions-diagnostics-template.md#wadmetrics-tables-in-storage).
* **StorageAccount** element je třeba aktualizovat s názvem účtu úložiště diagnostiky.
  
    ```
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Další postup
* Další pokyny týkající se použití možnosti diagnostiky Azure a další metody pro řešení problémů najdete v tématu [povolení diagnostiky Azure Cloud Services a virtuálních počítačů](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Schéma konfigurace diagnostiky](https://msdn.microsoft.com/library/azure/mt634524.aspx) vysvětluje různé možnosti konfigurace XML pro rozšíření diagnostiky.

