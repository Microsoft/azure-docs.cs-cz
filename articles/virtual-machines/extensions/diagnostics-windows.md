---
title: Použití Azure PowerShell k povolení diagnostiky na virtuálním počítači s Windows
description: Naučte se používat PowerShell k povolení Azure Diagnostics ve virtuálním počítači s Windows.
ms.topic: article
author: amjads1
ms.author: amjads
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.date: 12/15/2015
ms.openlocfilehash: e242a45e2c0f8247a1c23bf4d43a6e8b0a033d9b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560077"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Použití PowerShellu k povolení Azure Diagnostics ve virtuálním počítači s Windows

Azure Diagnostics je schopnost v Azure, která umožňuje shromažďování diagnostických dat v nasazené aplikaci. Diagnostické rozšíření můžete použít ke shromažďování diagnostických dat, jako jsou protokoly aplikací nebo čítače výkonu z virtuálního počítače Azure s Windows. 

 

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Povolit rozšíření diagnostiky, pokud používáte model nasazení Správce prostředků
Diagnostické rozšíření můžete povolit během vytváření virtuálního počítače s Windows pomocí modelu nasazení Azure Resource Manager přidáním konfigurace rozšíření do šablony Správce prostředků. V tématu [Vytvoření virtuálního počítače s Windows pomocí monitorování a diagnostiky použijte šablonu Azure Resource Manager](diagnostics-template.md).

Pokud chcete povolit diagnostické rozšíření na existujícím virtuálním počítači vytvořeném pomocí modelu nasazení Správce prostředků, můžete použít rutinu [set-AzVMDiagnosticsExtension](/powershell/module/az.compute/set-azvmdiagnosticsextension) prostředí PowerShell, jak je znázorněno níže.

```azurepowershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path
```


*$diagnosticsconfig _path* je cesta k souboru, který obsahuje konfiguraci diagnostiky ve formátu XML, jak je popsáno v následující [ukázce](#sample-diagnostics-configuration) .  

Pokud konfigurační soubor diagnostiky určuje element **StorageAccount** s názvem účtu úložiště, pak skript *set-AzVMDiagnosticsExtension* automaticky nastaví diagnostické rozšíření pro odesílání diagnostických dat do daného účtu úložiště. Aby to fungovalo, musí být účet úložiště ve stejném předplatném jako virtuální počítač.

Pokud se v konfiguraci diagnostiky nezadal žádný **StorageAccount** , musíte do rutiny předat parametr *StorageAccountName* . Je-li zadán parametr *StorageAccountName* , rutina vždy použije účet úložiště, který je zadán v parametru, a nikoli ten, který je zadán v konfiguračním souboru diagnostiky.

Pokud je účet úložiště diagnostiky v jiném předplatném, než je virtuální počítač, musíte explicitně předat parametry *StorageAccountName* a *StorageAccountKey* do rutiny. Parametr *StorageAccountKey* není potřeba, pokud je účet úložiště diagnostiky ve stejném předplatném, protože se rutina může automaticky dotazovat a nastavit hodnotu klíče při povolování diagnostického rozšíření. Pokud je však účet úložiště diagnostiky v jiném předplatném, rutina nemusí být schopna získat klíč automaticky a Vy musíte explicitně zadat klíč prostřednictvím parametru *StorageAccountKey* .  

```azurepowershell
Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

Jakmile je na virtuálním počítači povolené rozšíření diagnostiky, můžete získat aktuální nastavení pomocí rutiny [Get-AzVmDiagnosticsExtension](/powershell/module/az.compute/get-azvmdiagnosticsextension) .

```azurepowershell
Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name
```

Rutina vrátí *PublicSettings*, která obsahuje konfiguraci diagnostiky. Existují dva druhy konfigurace, WadCfg a xmlCfg. WadCfg je konfigurace JSON a xmlCfg je konfigurace XML ve formátu kódovaném v kódování Base64. Chcete-li si XML přečíst, je nutné ho dekódovat.

```azurepowershell
$publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
$encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
$xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
Write-Host $xmlconfig
```

Pomocí rutiny [Remove-AzVmDiagnosticsExtension](/powershell/module/az.compute/remove-azvmdiagnosticsextension) můžete odebrat diagnostické rozšíření z virtuálního počítače.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Povolit rozšíření diagnostiky, pokud používáte klasický model nasazení

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Pomocí rutiny [set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) můžete povolit diagnostické rozšíření na virtuálním počítači, který vytvoříte prostřednictvím modelu nasazení Classic. Následující příklad ukazuje, jak vytvořit nový virtuální počítač pomocí modelu nasazení Classic s povoleným rozšířením Diagnostika.

```azurepowershell
$VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
$VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
$VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM
```

Pokud chcete povolit diagnostické rozšíření na stávajícím virtuálním počítači, který se vytvořil prostřednictvím modelu nasazení Classic, použijte k získání konfigurace virtuálního počítače rutinu [Get-AzureVM](/powershell/module/servicemanagement/azure.service/get-azurevm) . Potom aktualizujte konfiguraci virtuálních počítačů tak, aby zahrnovala diagnostické rozšíření pomocí rutiny [set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) . Nakonec použijte aktualizovanou konfiguraci pro virtuální počítač pomocí [Update-AzureVM](/powershell/module/servicemanagement/azure.service/update-azurevm).

```azurepowershell
$VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
$VM_Update = Set-AzureVMDiagnosticsExtension  -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM
```

## <a name="sample-diagnostics-configuration"></a>Ukázková konfigurace diagnostiky
Následující kód XML lze použít pro veřejnou konfiguraci diagnostiky s výše uvedenými skripty. Tato ukázková konfigurace převede různé čítače výkonu na účet úložiště diagnostiky spolu s chybami z aplikace, zabezpečení a systémových kanálů v protokolech událostí systému Windows a případné chyby z protokolů diagnostické infrastruktury.

Konfigurace se musí aktualizovat, aby obsahovala následující:

* Atribut *ResourceID* elementu **Metrics** se musí aktualizovat s ID prostředku pro virtuální počítač.
  
  * ID prostředku se dá sestavit pomocí následujícího vzoru:/subscriptions/{*ID předplatného pro předplatné s virtuálním počítačem*}/RESOURCEGROUPS/{název *virtuálního**počítače*}/Providers/Microsoft.COMPUTE/virtualMachines/{.
  * Pokud je třeba ID předplatného pro předplatné, kde je spuštěný virtuální počítač, **11111111-1111-1111-1111-111111111111**, název skupiny prostředků pro skupinu prostředků je **MyResourceGroup** a název virtuálního počítače je **MyWindowsVM**, pak bude hodnota *ResourceID* :
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Další informace o tom, jak se generují metriky na základě konfigurace čítačů výkonu a metrik, najdete v tématu [Azure Diagnostics tabulce metrik v úložišti](diagnostics-template.md#wadmetrics-tables-in-storage).
* Element **StorageAccount** se musí aktualizovat názvem účtu úložiště diagnostiky.
  
    ```xml
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

## <a name="next-steps"></a>Další kroky
* Další informace o používání funkce Azure Diagnostics a dalších technik k řešení problémů najdete v tématu [Povolení diagnostiky v Azure Cloud Services a Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Schéma konfigurací diagnostiky](../../azure-monitor/agents/diagnostics-extension-versions.md) vysvětluje různé možnosti konfigurace XML pro diagnostické rozšíření.