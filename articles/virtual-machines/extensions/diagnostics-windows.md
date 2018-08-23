---
title: Povolit diagnostiku na virtuálním počítači s Windows pomocí Azure Powershellu | Dokumentace Microsoftu
services: virtual-machines-windows
documentationcenter: ''
description: Další informace o použití Powershellu k povolení diagnostiky Azure v rámci virtuálního počítače se systémem Windows
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
ms.openlocfilehash: 2a4f55ea15c933094befb8855185c4b7e353dee3
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054619"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Použití PowerShellu k povolení Diagnostiky Azure na virtuálním počítači s Windows

Diagnostika Azure je funkce v Azure, která umožňuje shromažďování diagnostických dat v nasazené aplikaci. Diagnostické rozšíření můžete použít ke shromažďování diagnostických dat, jako jsou protokoly aplikací a čítače výkonu z virtuálního počítače Azure (VM), na kterém běží Windows. Tento článek popisuje, jak povolit rozšíření diagnostiky pro virtuální počítač pomocí Windows Powershellu. Zobrazit [instalace a konfigurace Azure Powershellu](/powershell/azure/overview) pro součásti potřebné pro účely tohoto článku.

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Povolení diagnostického rozšíření, pokud používáte model nasazení Resource Manager
Při vytváření virtuálního počítače s Windows prostřednictvím modelu nasazení Azure Resource Manageru tak, že přidáte konfigurace rozšíření šablony Resource Manageru, můžete povolit rozšíření diagnostiky. Zobrazit [vytvořit virtuální počítač Windows s monitorováním a diagnostikou pomocí šablony Azure Resource Manageru](diagnostics-template.md).

Pokud chcete povolit rozšíření diagnostiky na existující virtuální počítač, který byl vytvořen pomocí modelu nasazení Resource Manager, můžete použít [Set-AzureRMVMDiagnosticsExtension](/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension) rutiny Powershellu, jak je znázorněno níže.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* je cesta k souboru, který obsahuje konfiguraci diagnostiky ve formátu XML, jak je popsáno v [ukázka](#sample-diagnostics-configuration) níže.  

Pokud konfigurační soubor diagnostiky má **StorageAccount** element s názvem účtu úložiště, pak bude *Set-AzureRMVMDiagnosticsExtension* skriptu automaticky nastaví Diagnostika rozšíření posílat diagnostická data do účtu úložiště. Aby to fungovalo musí být ve stejném předplatném jako virtuální počítač účtu úložiště.

Pokud ne **StorageAccount** byl zadaný v konfiguraci diagnostiky, je nutné předat *StorageAccountName* do rutiny parametr. Pokud *StorageAccountName* zadaný parametr, bude rutina bude vždy používat účet úložiště, který je zadaný v parametru a není ten, který je zadaný v konfigurační soubor diagnostiky.

Pokud je účet úložiště diagnostiky v jiném předplatném než virtuální počítač, je nutné explicitně předávat *StorageAccountName* a *StorageAccountKey* parametry rutiny. *StorageAccountKey* parametr není nutná, pokud účet úložiště diagnostiky je ve stejném předplatném, protože rutina automaticky můžete zadávat dotazy a nastavte hodnotu klíče při povolování rozšíření diagnostiky. Pokud je účet úložiště diagnostiky v jiném předplatném, pak rutina nemusí být možné získat klíč automaticky a je potřeba explicitně zadat klíč prostřednictvím *StorageAccountKey* parametru.  

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Po povolení diagnostického rozšíření ve virtuálním počítači můžete získat aktuální nastavení pomocí [Get-AzureRMVmDiagnosticsExtension](/powershell/module/azurerm.compute/get-azurermvmdiagnosticsextension) rutiny.

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

Vrátí rutina *PublicSettings*, který obsahuje konfiguraci diagnostiky. Existují dva typy konfigurace podporovaná, WadCfg a xmlCfg. WadCfg je konfigurace JSON a xmlCfg je konfigurace XML ve formátu s kódováním Base64. Chcete-li čtení dat XML, dekódovat.

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

[Odebrat AzureRMVmDiagnosticsExtension](/powershell/module/azurerm.compute/remove-azurermvmdiagnosticsextension) rutina slouží k odebrání diagnostické rozšíření z virtuálního počítače.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Povolení diagnostického rozšíření, pokud používáte model nasazení classic
Můžete použít [Set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) rutina pro povolení diagnostického rozšíření na virtuální počítač, který lze vytvořit pomocí modelu nasazení classic. Následující příklad ukazuje, jak vytvořit nový virtuální počítač prostřednictvím modelu nasazení classic s diagnostickým rozšířením povolena.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Pokud chcete povolit rozšíření diagnostiky na existující virtuální počítač, který byl vytvořen prostřednictvím modelu nasazení classic, nejprve pomocí [Get-AzureVM](/powershell/module/servicemanagement/azure/get-azurevm) rutiny pro získání konfigurace virtuálního počítače. Aktualizujte konfiguraci virtuálního počítače pro zahrnutí rozšíření diagnostiky pomocí [Set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) rutiny. Nakonec platí aktualizovanou konfiguraci pro virtuální počítač s použitím [Update-AzureVM](/powershell/module/servicemanagement/azure/update-azurevm).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Ukázková konfigurace diagnostiky
Následující kód XML lze použít pro veřejné konfiguraci diagnostiky pomocí výše uvedených skriptů. Tahle vzorová konfigurace přenášet různé čítače výkonu pro účet úložiště diagnostiky, spolu s chyby z aplikace, zabezpečení a systém kanálů v protokolu událostí Windows a případné chyby protokoly infrastruktury diagnostiky.

Konfigurace se musí aktualizovat tak, aby zahrnují následující:

* *ResourceID* atribut **metriky** element je potřeba aktualizovat s ID prostředku pro virtuální počítač.
  
  * ID prostředku lze sestavit pomocí následujícímu vzoru: "/ subscriptions / {*ID odběru pro odběr s virtuálním Počítačem*} /resourceGroups/ {*název skupiny prostředků pro virtuální počítač*} / providers/Microsoft.Compute/virtualMachines/ {*název virtuálního počítače*} ".
  * Například pokud ID předplatného pro předplatné, ve kterém je spuštěný virtuální počítač je **11111111-1111-1111-1111-111111111111**, název skupiny prostředků pro skupinu prostředků je **MyResourceGroup**a Název virtuálního počítače je **MyWindowsVM**, pak hodnota *resourceID* by být:
    
      ```
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Další informace o tom, metriky jsou generovány na základě konfigurace metriky a čítače výkonu, přečtěte si téma [tabulky Azure Diagnostics metriky ve službě storage](diagnostics-template.md#wadmetrics-tables-in-storage).
* **StorageAccount** element je potřeba aktualizovat s názvem účet úložiště diagnostiky.
  
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
* Další informace o použití funkce Azure Diagnostics a další techniky k řešení problémů naleznete v tématu [povolení diagnostiky v Azure Cloud Services a Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Schéma konfigurace diagnostiky](https://msdn.microsoft.com/library/azure/mt634524.aspx) vysvětluje různé možnosti konfigurace XML pro rozšíření diagnostiky.

