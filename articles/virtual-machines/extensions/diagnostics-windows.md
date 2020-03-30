---
title: Povolení diagnostiky na virtuálním počítači s Windows pomocí Azure PowerShellu
services: virtual-machines-windows
documentationcenter: ''
description: Zjistěte, jak pomocí PowerShellu povolit Diagnostiku Azure ve virtuálním počítači se systémem Windows.
author: sbtron
manager: gwallace
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2015
ms.author: saurabh
ms.openlocfilehash: 55afeb52323ead7db8be7e8fd1dabc880328e888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921534"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Povolení diagnostiky Azure ve virtuálním počítači s Windows pomocí PowerShellu

Diagnostika Azure je funkce v rámci Azure, která umožňuje shromažďování diagnostických dat v nasazené aplikaci. Rozšíření diagnostiky můžete použít ke shromažďování diagnostických dat, jako jsou protokoly aplikací nebo čítače výkonu z virtuálního počítače Azure ,, se systémem Windows. 

 

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Povolení rozšíření diagnostiky, pokud používáte model nasazení Správce prostředků
Rozšíření diagnostiky můžete povolit při vytváření virtuálního počítače s Windows prostřednictvím modelu nasazení Azure Resource Manager přidáním konfigurace rozšíření do šablony Správce prostředků. Viz [Vytvoření virtuálního počítače s Windows s monitorováním a diagnostikou pomocí šablony Azure Resource Manager](diagnostics-template.md).

Chcete-li povolit rozšíření diagnostiky na existující ms, který byl vytvořen prostřednictvím modelu nasazení Resource Manager, můžete použít [Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiagnosticsextension) PowerShell rutina, jak je znázorněno níže.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_cesta* je cesta k souboru, který obsahuje konfiguraci diagnostiky v XML, jak je popsáno v [následující ukázce.](#sample-diagnostics-configuration)  

Pokud konfigurační soubor diagnostiky určuje prvek **StorageAccount** s názvem účtu úložiště, skript *Set-AzVMDiagnosticsExtension* automaticky nastaví rozšíření diagnostiky pro odesílání diagnostických dat do tohoto účtu úložiště. Aby to fungovalo, musí být účet úložiště ve stejném předplatném jako virtuální hod.

Pokud v konfiguraci diagnostiky nebyl zadán žádný **účet úložiště,** musíte mu předat parametr *StorageAccountName* do rutiny. Pokud *storageAccountName* parametr je zadán, pak rutina bude vždy používat účet úložiště, který je zadán v parametru a nikoli ten, který je zadán v diagnostickém konfiguračním souboru.

Pokud je účet úložiště diagnostiky v jiném předplatném než virtuální hod, musíte explicitně předat parametry *StorageAccountName* a *StorageAccountKey* do rutiny. *StorageAccountKey* Parametr není potřeba, pokud je účet úložiště diagnostiky ve stejném předplatném, protože rutina může automaticky dotazovat a nastavit hodnotu klíče při povolení rozšíření diagnostiky. Pokud je však účet úložiště diagnostiky v jiném předplatném, rutina nemusí být schopna získat klíč automaticky a je třeba explicitně zadat klíč prostřednictvím parametru *StorageAccountKey.*  

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Jakmile je rozšíření diagnostiky povoleno na virtuálním počítači, můžete získat aktuální nastavení pomocí rutiny [Get-AzVmDiagnosticsExtension.](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiagnosticsextension)

    Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

Rutina vrátí *PublicSettings*, který obsahuje konfiguraci diagnostiky. Jsou podporovány dva druhy konfigurace, WadCfg a xmlCfg. WadCfg je konfigurace JSON a xmlCfg je konfigurace XML ve formátu kódu Base64. Chcete-li číst XML, musíte jej dekódovat.

    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

Rutina [Remove-AzVmDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdiagnosticsextension) slouží k odebrání rozšíření diagnostiky z virtuálního trhu.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Povolení rozšíření diagnostiky, pokud používáte klasický model nasazení

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Rutina [Set-AzureVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) můžete použít k povolení rozšíření diagnostiky na virtuálním počítači, který vytvoříte prostřednictvím modelu klasického nasazení. Následující příklad ukazuje, jak vytvořit nový virtuální počítače prostřednictvím klasického modelu nasazení s povoleným rozšířením diagnostiky.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM

Chcete-li povolit rozšíření diagnostiky na existující virtuální počítač, který byl vytvořen prostřednictvím modelu klasické nasazení, nejprve použijte [rutinu Get-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azurevm) získat konfiguraci virtuálního počítače. Pak aktualizujte konfiguraci virtuálního počítače tak, aby zahrnovala rozšíření diagnostiky pomocí rutiny [Set-AzureVMDiagnosticsExtension.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) Nakonec použijte aktualizovanou konfiguraci na virtuální počítač pomocí [Update-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/update-azurevm).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension  -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Konfigurace diagnostiky vzorků
Následující XML lze použít pro diagnostiku veřejné konfigurace s výše uvedenými skripty. Tato ukázková konfigurace přenese různé čítače výkonu do účtu úložiště diagnostiky spolu s chybami z aplikačních, zabezpečovacích a systémových kanálů v protokolech událostí systému Windows a všemi chybami z protokolů infrastruktury diagnostiky.

Konfigurace musí být aktualizována tak, aby zahrnovala následující:

* Atribut *resourceID* prvku **Metriky** musí být aktualizován id prostředku pro virtuální počítač.
  
  * ID prostředku lze sestavit pomocí následujícího vzoru: "/subscriptions/{*ID předplatného s virtuálním počítačem*}/resourceGroups/{*Název skupiny prostředků pro virtuální počítač*}/providers/Microsoft.Compute/virtualMachines/{ Název*virtuálního počítače*}".
  * Například pokud ID předplatného pro odběr, kde je spuštěn virtuální hod **111111111-1111-1111-1111-11111111111111 ,** název skupiny prostředků pro skupinu prostředků je **MyResourceGroup**a název virtuálního měna je **MyWindowsVM**, pak hodnota *resourceID* by:
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Další informace o tom, jak se metriky generují na základě čítačů výkonu a konfigurace metrik, najdete [v tématu tabulka metrik Diagnostika Azure v úložišti](diagnostics-template.md#wadmetrics-tables-in-storage).
* Prvek **StorageAccount** musí být aktualizován s názvem účtu úložiště diagnostiky.
  
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
* Další pokyny k použití funkce Diagnostika Azure a další techniky k řešení problémů najdete [v tématu Povolení diagnostiky v Cloudových službách Azure a virtuálních počítačích](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Schéma konfigurace diagnostiky](https://msdn.microsoft.com/library/azure/mt634524.aspx) vysvětluje různé možnosti konfigurace XML pro rozšíření diagnostiky.

