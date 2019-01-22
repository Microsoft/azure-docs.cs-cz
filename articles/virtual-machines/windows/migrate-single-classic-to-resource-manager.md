---
title: Migrace klasického virtuálního počítače na spravovaného disku virtuálního počítače ARM | Dokumentace Microsoftu
description: Migrace jednoho virtuálního počítače Azure z modelu nasazení classic na Managed Disks v modelu nasazení Resource Manager.
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
ms.openlocfilehash: b69cc0bcff8f791e0740b30f5521a2a7b36af3b1
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437653"
---
# <a name="migrate-a-classic-vm-to-use-a-managed-disk"></a>Migrace klasického virtuálního počítače pro použití spravovaného disku 


V této části vám umožní migrovat stávající virtuální počítače Azure z modelu nasazení classic na [Managed Disks](managed-disks-overview.md) v modelu nasazení Resource Manager.


## <a name="plan-for-the-migration-to-managed-disks"></a>Plánování migrace do služby Managed Disks

V této části vám umožní nejlepší rozhodnutí o typech virtuálních počítačů a disk.


### <a name="location"></a>Umístění

Vyberte umístění, kde jsou k dispozici Managed Disks. Pokud provádíte migraci na spravovaný Disk storage úrovně Premium se opírá o, ujistěte se také, že Premium storage je dostupné v dané oblasti. Zobrazit [Azure Services byRegion](https://azure.microsoft.com/regions/#services) aktuální informace o dostupných umístění.

### <a name="vm-sizes"></a>Velikost virtuálních počítačů

Pokud provádíte migraci do služby Managed Disks použití služby Premium storage, budete muset aktualizovat velikost virtuálního počítače do Premium Storage podporuje velikost dostupná v oblasti, kde je umístěn virtuální počítač. Kontrola velikosti virtuálních počítačů, které jsou schopné Premium Storage. Specifikace velikosti virtuálního počítače Azure jsou uvedeny v [velikosti virtuálních počítačů](sizes.md).
Zkontrolujte charakteristiky výkonu virtuálních počítačů, které pracují se službou Premium Storage a zvolte nejvhodnější velikosti virtuálního počítače, který nejlépe vyhovuje vaší úlohy. Ujistěte se, že je dostatečná šířka pásma dostupné na virtuálním počítači Centrum umožňující prosazovat diskové přenosy.

### <a name="disk-sizes"></a>Velikost disků

**Premium**

Existuje sedm typů úložiště Premium Storage, který lze použít s virtuálním Počítačem a obsahují konkrétní IOPs a propustnost omezení. Při výběru disk typu Premium pro virtuální počítač na základě potřeb vaší aplikace z hlediska kapacity, výkon, škálovatelnost, zvažte tato omezení a načte ve špičce.

| Typ disky Premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Velikost disku           | 128 GB| 512 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Vstupně-výstupní operace za sekundu / disk       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Propustnost / disk | 25 MB za sekundu  | 50 MB za sekundu  | 100 MB za sekundu | 150 MB za sekundu | 200 MB za sekundu | 250 MB za sekundu | 250 MB za sekundu | 

**Standard**

Existuje sedm typů standardní disky, které lze použít s virtuálním Počítačem. Každý z nich mít jiné kapacitě, ale mají stejné vstupně-výstupních operací a omezení propustnosti. Zvolte typ služby Standard Managed disks na základě potřeb kapacity vaší aplikace.

| Disk typu Standard  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| Velikost disku           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| Vstupně-výstupní operace za sekundu / disk       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| Propustnost / disk | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 60 MB za sekundu | 


### <a name="disk-caching-policy"></a>Zásady ukládání do mezipaměti na disku 

**Premium Managed Disks**

Ve výchozím nastavení, disk zásady ukládání do mezipaměti je *jen pro čtení* pro všechny úrovně Premium datové disky, a *čtení a zápis* pro disk s operačním systémem Premium připojené k virtuálnímu počítači. Toto nastavení konfigurace se doporučuje pro zajištění optimálního výkonu pro vaše aplikace IOs. Náročné na zápis nebo jen pro zápis datové disky (jako jsou například soubory protokolu serveru SQL Server) Zakázání používání mezipaměti disku, takže můžete dosáhnout lepší výkon aplikace.

### <a name="pricing"></a>Ceny

Zkontrolujte [ceny pro Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). Ceny za spravované disky úrovně Premium je stejná jako disky úrovně premium. Ale ceny za spravované disky úrovně standard se liší od nespravované disky standard.


## <a name="checklist"></a>Kontrolní seznam

1.  Pokud provádíte migraci do služby Managed Disks úrovně Premium, ujistěte se, že je k dispozici v oblasti, kterou provádíte migraci do.

2.  Rozhodněte, nových řadách virtuálních počítačů, které budete používat. Pokud provádíte migraci na Managed Disks úrovně Premium, měla by být Premium Storage podporuje.

3.  Rozhodněte, přesnou velikost virtuálního počítače, které se použijí, které jsou dostupné v oblastech, které při migraci na. Velikost virtuálního počítače musí být dostatečně velký, aby podporoval počet datových disků, které máte. Například pokud máte čtyři datové disky, virtuální počítač musí mít dva nebo více jader. Zvažte také zpracování výkon, paměť a požadavky na šířku pásma sítě.

4.  Máte aktuální podrobnosti o virtuálním počítači, které jsou po ruce, včetně seznamu disků a odpovídající objekty BLOB VHD.

Příprava aplikace pro výpadek. Pokud chcete provést čistou migrace, je nutné zastavit veškeré zpracování v aktuálním systému. Teprve pak můžete získat do konzistentního stavu, které můžete migrovat na novou platformu. Doba trvání výpadku závisí na množství dat na discích, které chcete migrovat.


## <a name="migrate-the-vm"></a>Migrace virtuálního počítače

Příprava aplikace pro výpadek. Pokud chcete provést čistou migrace, je nutné zastavit veškeré zpracování v aktuálním systému. Teprve pak můžete získat do konzistentního stavu, které můžete migrovat na novou platformu. Doba trvání výpadku závisí množství dat na discích, které chcete migrovat.

Tato část vyžaduje modul Azure PowerShell verze 6.0.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Musíte také spustit příkaz `Connect-AzureRmAccount`, abyste vytvořili připojení k Azure.


Vytváření proměnných pro společné parametry.

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

Vytvoření spravovaného disku operačního systému pomocí virtuálního pevného disku z klasického virtuálního počítače. Ujistěte se, zda jste zadali úplný identifikátor URI virtuálního pevného disku operačního systému k parametru $osVhdUri. Také zadejte **- AccountType** jako **Premium_LRS** nebo **Standard_LRS** založené na typu disků (premium nebo standard) při migraci na.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName '
   -Disk (New-AzureRmDiskConfig '
   -AccountType Premium_LRS '
   -Location $location '
   -CreateOption Import '
   -SourceUri $osVhdUri) '
   -ResourceGroupName $resourceGroupName
```

Připojte disk s operačním systémem k novému virtuálnímu počítači.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
$VirtualMachine = Set-AzureRmVMOSDisk '
   -VM $VirtualMachine '
   -ManagedDiskId $osDisk.Id '
   -StorageAccountType Premium_LRS '
   -DiskSizeInGB 128 '
   -CreateOption Attach -Windows
```

Vytvoření spravovaného datového disku z datového souboru virtuálního pevného disku a přidejte ho do nového virtuálního počítače.

```powershell
$dataDisk1 = New-AzureRmDisk '
   -DiskName $dataDiskName '
   -Disk (New-AzureRmDiskConfig '
   -AccountType Premium_LRS '
   -Location $location '
   -CreationOption Import '
   -SourceUri $dataVhdUri ) '
   -ResourceGroupName $resourceGroupName
    
$VirtualMachine = Add-AzureRmVMDataDisk '
   -VM $VirtualMachine '
   -Name $dataDiskName '
   -CreateOption Attach '
   -ManagedDiskId $dataDisk1.Id '
   -Lun 1
```

Vytvořit nový virtuální počítač tak, že nastavíte veřejné IP adresy, virtuální sítě a síťovou kartu

```powershell
$publicIp = New-AzureRmPublicIpAddress '
   -Name ($VirtualMachineName.ToLower()+'_ip') '
   -ResourceGroupName $resourceGroupName '
   -Location $location '
   -AllocationMethod Dynamic
    
$vnet = Get-AzureRmVirtualNetwork '
   -Name $virtualNetworkName '
   -ResourceGroupName $resourceGroupName
    
$nic = New-AzureRmNetworkInterface '
   -Name ($VirtualMachineName.ToLower()+'_nic') '
   -ResourceGroupName $resourceGroupName '
   -Location $location '
   -SubnetId $vnet.Subnets[0].Id '
   -PublicIpAddressId $publicIp.Id
    
$VirtualMachine = Add-AzureRmVMNetworkInterface '
   -VM $VirtualMachine '
   -Id $nic.Id
    
New-AzureRmVM -VM $VirtualMachine '
   -ResourceGroupName $resourceGroupName '
   -Location $location
```

> [!NOTE]
>Můžou existovat další kroky nutné k podpoře vaší aplikace, která je jako nepokrytý této příručce.
>
>

## <a name="next-steps"></a>Další postup

- Připojte se k virtuálnímu počítači. Pokyny najdete v tématu [jak se připojit a přihlaste se na virtuálním počítači Azure s Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

