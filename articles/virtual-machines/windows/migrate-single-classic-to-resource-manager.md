---
title: Migrace klasické virtuální počítač do virtuálních počítačů spravovaných disků ARM | Microsoft Docs
description: Migrujte jeden virtuální počítač Azure z modelu nasazení classic k spravovaná diskům v modelu nasazení Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: d0307b26741a6bbbf29626e670467cdd72697646
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="manually-migrate-a-classic-vm-to-a-new-arm-managed-disk-vm-from-the-vhd"></a>Ruční migraci Classic virtuálního počítače do nového ARM spravované disku virtuálního počítače z virtuálního pevného disku 


V této části vám umožňuje migrovat existující virtuální počítače Azure z modelu nasazení classic do [spravované disky](managed-disks-overview.md) v modelu nasazení Resource Manager.


## <a name="plan-for-the-migration-to-managed-disks"></a>Plánování migrace na spravované disky

V této části můžete vytvořit nejlepší rozhodnutí o typech virtuálního počítače a disku.


### <a name="location"></a>Umístění

Vyberte umístění, kde Azure spravované disky jsou dostupné. Při migraci disků spravovaných Premium také zajistíte, že úložiště Premium je k dispozici v oblasti, kde máte v úmyslu migrovat. V tématu [služeb Azure byRegion](https://azure.microsoft.com/regions/#services) aktuální informace o dostupných umístění.

### <a name="vm-sizes"></a>Velikost virtuálních počítačů

Pokud provádíte migraci na disky spravované Premium, budete muset aktualizovat velikost virtuálního počítače do Storage úrovně Premium podporující velikost dostupné v oblasti, kde je umístěn virtuální počítač. Zkontrolujte velikosti virtuálních počítačů, které jsou podporující Storage úrovně Premium. Specifikace velikosti virtuálního počítače Azure, jsou uvedeny v [velikosti virtuálních počítačů](sizes.md).
Zkontrolujte vlastnosti výkonu virtuálních počítačů, které pracovat Storage úrovně Premium a vyberete nejvhodnější velikost virtuálního počítače, který nejlépe vyhovuje vaší zatížení. Ujistěte se, že je dostatečnou šířku pásma dostupné na vašem virtuálním počítači k řízení provozu disku.

### <a name="disk-sizes"></a>Velikost disků

**Premium spravované disky**

Existují sedm typy disků spravované premium, které lze použít s vaší virtuálních počítačů a každá z nich má konkrétní IOPs a propustnost omezení. Vezměte v úvahu tyto limity při výběru typu Premium disku pro virtuální počítač na základě potřeb vaší aplikace z hlediska kapacity, výkon, škálovatelnost a načte ve špičce.

| Disky typu Premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Velikost disku           | 128 GB| 512 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Vstupně-výstupní operace za sekundu / disk       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Propustnost / disk | 25 MB za sekundu  | 50 MB za sekundu  | 100 MB za sekundu | 150 MB za sekundu | 200 MB za sekundu | 250 MB za sekundu | 250 MB za sekundu | 

**Standardní disky spravované**

Existují sedm typy spravované standardní disky, které lze použít s virtuálního počítače. Každý z nich mají různé kapacity ale mít stejné IOPS a omezení propustnosti. Vyberte typ standardní spravovaných disků na základě potřeb kapacitu vaší aplikace.

| Disk typu Standard  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| Velikost disku           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| Vstupně-výstupní operace za sekundu / disk       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| Propustnost / disk | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 


### <a name="disk-caching-policy"></a>Zásady ukládání do mezipaměti na disku 

**Premium spravované disky**

Ve výchozím nastavení, je disk ukládání do mezipaměti zásad *jen pro čtení* pro všechny Premium datových disků, a *pro čtení a zápis* pro disk operačního systému Premium připojen k virtuálnímu počítači. Toto nastavení konfigurace se doporučuje pro dosažení optimálního výkonu pro vaše aplikace IOs. Těžký zápisu nebo pouze pro zápis datové disky (například soubory protokolu serveru SQL Server) zakažte ukládání do mezipaměti disku, takže můžete dosáhnout lepší výkon aplikace.

### <a name="pricing"></a>Ceny

Zkontrolujte [ceny pro spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/). Ceny disků spravované Premium je stejný jako nespravované prémiové disky. Ale ceny pro standardní disky spravované se liší od standardní nespravované disky.


## <a name="checklist"></a>Kontrolní seznam

1.  Pokud provádíte migraci na prémiové disky spravovat, zkontrolujte, zda že je k dispozici v oblast, kterou provádíte migraci do.

2.  Rozhodněte, nové řadu virtuálních počítačů, kterou budete používat. Pokud provádíte migraci na prémiové disky spravované by mělo být schopný úložiště Premium.

3.  Rozhodněte, přesný velikost virtuálního počítače, které budete používat, které jsou k dispozici v oblast, kterou provádíte migraci do. Velikost virtuálního počítače musí být dostatečně velký pro podporu většímu počtu datových disků, které máte. Například pokud máte čtyři datové disky, virtuální počítač musí mít dva nebo víc jader. Zvažte také výpočetní výkon, paměti a šířky pásma sítě musí.

4.  Máte aktuální podrobnosti o virtuálních počítačů, které jsou užitečné, včetně seznamu disků a odpovídající BLOB VHD.

Připravte aplikaci výpadek. K provedení čisté migrace, budete muset zastavit veškeré zpracování v aktuálním systému. Pak můžete získat do konzistentního stavu, které můžete migrovat na nové platformě. Doba trvání výpadku závisí na množství dat na discích k migraci.


## <a name="migrate-the-vm"></a>Migraci virtuálního počítače

Připravte aplikaci výpadek. K provedení čisté migrace, budete muset zastavit veškeré zpracování v aktuálním systému. Pak můžete získat do konzistentního stavu, které můžete migrovat na nové platformě. Doba trvání výpadku závisí množství dat na discích k migraci.

Tato část vyžaduje prostředí Azure PowerShell verze modulu 6.0.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Budete také muset spustit `Connect-AzureRmAccount` vytvořit připojení s Azure.


1.  Nastavte nejprve, běžné parametry:

    ```powershell
    $resourceGroupName = 'yourResourceGroupName'
    
    $location = 'your location' 
    
    $virtualNetworkName = 'yourExistingVirtualNetworkName'
    
    $virtualMachineName = 'yourVMName'
    
    $virtualMachineSize = 'Standard_DS3'
    
    $adminUserName = "youradminusername"
    
    $adminPassword = "yourpassword" | ConvertTo-SecureString -AsPlainText -Force
    
    $imageName = 'yourImageName'
    
    $osVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
    
    $dataVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk1.vhd'
    
    $dataDiskName = 'dataDisk1'
    ```

2.  Vytvoření spravovaného disku operačního systému pomocí virtuálního pevného disku z klasického virtuálního počítače.

    Zkontrolujte, zda jste zadali úplný identifikátor URI virtuálního pevného disku operačního systému na parametr $osVhdUri. Zadejte také **- AccountType** jako **Premium_LRS** nebo **Standard_LRS** na základě typu disků (Standard nebo Premium) při migraci do.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType Premium_LRS -Location $location -CreateOption Import -SourceUri $osVhdUri) '
    -ResourceGroupName $resourceGroupName
    ```

3.  Připojte disk operačního systému na nový virtuální počítač.

    ```powershell
    $VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $osDisk.Id '
    -StorageAccountType Premium_LRS -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

4.  Vytvoření disku spravovaná data z datového souboru virtuálního pevného disku a přidat jej do nového virtuálního počítače.

    ```powershell
    $dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType Premium_LRS -Location $location -CreationDataCreateOption Import '
    -SourceUri $dataVhdUri ) -ResourceGroupName $resourceGroupName
    
    $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName '
    -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
    ```

5.  Vytvoření nového virtuálního počítače pomocí nastavení veřejné IP adresy, virtuální sítě a síťový adaptér.

    ```powershell
    $publicIp = New-AzureRmPublicIpAddress -Name ($VirtualMachineName.ToLower()+'_ip') '
    -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
    
    $vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName
    
    $nic = New-AzureRmNetworkInterface -Name ($VirtualMachineName.ToLower()+'_nic') '
    -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id '
    -PublicIpAddressId $publicIp.Id
    
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id
    
    New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
    ```

> [!NOTE]
>Mohou existovat další kroky nezbytné pro podporu vaší aplikace, která je nesmí být zahrnuté v této příručce.
>
>

## <a name="next-steps"></a>Další postup

- Připojte k virtuálnímu počítači. Pokyny najdete v tématu [jak se připojit a přihlásit se na virtuálním počítači Azure s Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

