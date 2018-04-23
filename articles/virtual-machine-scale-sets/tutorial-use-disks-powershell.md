---
title: Kurz – Vytvoření a použití disků pro škálovací sady pomocí Azure PowerShellu | Microsoft Docs
description: Zjistěte, jak pomocí Azure PowerShellu vytvořit a používat spravované disky se škálovacími sadami virtuálních počítačů, včetně přidání, přípravy, výpisu a odpojení disků.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: fd28b7e1f7407b1d1ee08c2f5774d939852e57b5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-azure-powershell"></a>Kurz: Vytvoření a použití disků se škálovací sadou virtuálních počítačů pomocí Azure PowerShellu
Škálovací sady virtuálních počítačů využívají disky k ukládání operačních systémů, aplikací a dat instancí virtuálních počítačů. Při vytváření a správě škálovací sady je důležité, abyste zvolili vhodnou velikost disku a konfiguraci pro očekávané úlohy. Tento kurz se zabývá vytvořením a správou disků virtuálních počítačů. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Disky s operačním systémem a dočasné disky
> * Datové disky
> * Disky Standard a Premium
> * Výkon disků
> * Připojení a příprava datových disků

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.6.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure. 


## <a name="default-azure-disks"></a>Výchozí disky v Azure
Při vytváření nebo škálování škálovací sady se ke každé instanci virtuálního počítače automaticky připojí dva disky. 

**Disk s operačním systémem** – Disky s operačním systémem můžou mít velikost až 2 TB a hostují operační systém instance virtuálního počítače. Disk s operačním systémem má ve výchozím nastavení popisek */dev/sda*. Konfigurace ukládání do mezipaměti na disku je u disku s operačním systémem optimalizovaná s ohledem na výkon operačního systému. Vzhledem k této konfiguraci byste na disk s operačním systémem **neměli** ukládat aplikace nebo data. Pro aplikace a data použijte datové disky, které podrobněji probíráme dále v tomto článku. 

**Dočasný disk** – Dočasné disky používají disk SSD, který je umístěný na stejném hostiteli Azure jako instance virtuálního počítače. Tyto disky mají vysoký výkon a můžou se používat pro operace, jako je zpracování dočasných dat. Pokud však dojde k přesunu instance virtuálního počítače na nového hostitele, všechna data uložená na dočasném disku se odeberou. Velikost dočasného disku se určuje podle velikosti instance virtuálního počítače. Dočasné disky mají popisek */dev/sdb* a mají přípojný bod */mnt*.

### <a name="temporary-disk-sizes"></a>Velikosti dočasného disku
| Typ | Běžné velikosti | Maximální velikost dočasného disku (GiB) |
|----|----|----|
| [Obecné účely](../virtual-machines/windows/sizes-general.md) | Řady A, B a D | 1600 |
| [Optimalizované z hlediska výpočetních služeb](../virtual-machines/windows/sizes-compute.md) | Řada F | 576 |
| [Optimalizované z hlediska paměti](../virtual-machines/windows/sizes-memory.md) | Řady D, E, G a M | 6144 |
| [Optimalizované z hlediska úložiště](../virtual-machines/windows/sizes-storage.md) | Řada L | 5630 |
| [GPU](../virtual-machines/windows/sizes-gpu.md) | Řada N | 1440 |
| [Vysoký výkon](../virtual-machines/windows/sizes-hpc.md) | Řady A a H | 2000 |


## <a name="azure-data-disks"></a>Datové disky Azure
Pokud potřebujete instalovat aplikace a ukládat data, můžete přidat další datové disky. Datové disky by se měly používat v každé situaci, kdy se vyžaduje odolné a responzivní úložiště dat. Každý datový disk má maximální kapacitu 4 TB. Velikost instance virtuálního počítače určuje, kolik datových disků je možné připojit. Na každý virtuální procesor virtuálního počítače je možné připojit dva datové disky.

### <a name="max-data-disks-per-vm"></a>Maximum datových disků na virtuální počítač
| Typ | Běžné velikosti | Maximum datových disků na virtuální počítač |
|----|----|----|
| [Obecné účely](../virtual-machines/windows/sizes-general.md) | Řady A, B a D | 64 |
| [Optimalizované z hlediska výpočetních služeb](../virtual-machines/windows/sizes-compute.md) | Řada F | 64 |
| [Optimalizované z hlediska paměti](../virtual-machines/windows/sizes-memory.md) | Řady D, E, G a M | 64 |
| [Optimalizované z hlediska úložiště](../virtual-machines/windows/sizes-storage.md) | Řada L | 64 |
| [GPU](../virtual-machines/windows/sizes-gpu.md) | Řada N | 64 |
| [Vysoký výkon](../virtual-machines/windows/sizes-hpc.md) | Řady A a H | 64 |


## <a name="vm-disk-types"></a>Typy disků virtuálního počítače
Azure poskytuje dva typy disků.

### <a name="standard-disk"></a>Disk Standard
Služba Storage úrovně Standard je založená na pevných discích a poskytuje nákladově efektivní úložiště a úroveň výkonu. Disky Standard jsou ideální pro nákladově efektivní vývoj a testování.

### <a name="premium-disk"></a>Disk Premium
Disky Premium jsou založené na vysoce výkonných discích SSD s nízkou latencí. Tyto disky se doporučují pro virtuální počítače, na kterých se spouští produkční úlohy. Služba Premium Storage podporuje virtuální počítače řad DS, DSv2, GS a FS. Při výběru se hodnota velikosti disku zaokrouhluje nahoru na nejbližší typ. Pokud je například velikost disku menší než 128 GB, typ disku je P10. Pokud je velikost disku mezi 129 až 512 GB, jde o typ (velikost) P20. V případě velikosti větší než 512 GB jde o typ P30.

### <a name="premium-disk-performance"></a>Výkon disků Premium
|Typ disku pro Premium Storage | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Velikost disku (zaokrouhluje se nahoru) | 32 GB | 64 GB | 128 GB | 512 GB | 1 024 GB (1 TB) | 2 048 GB (2 TB) | 4 095 GB (4 TB) |
| Maximum vstupně-výstupních operací za sekundu (IOPS) na disk | 120 | 240 | 500 | 2 300 | 5 000 | 7 500 | 7 500 |
Propustnost / disk | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

V tabulce výše se sice uvádí maximum vstupně-výstupních operací za sekundu (IOPS), ale prokládáním více datových disků je možné dosáhnout i vyšší úrovně výkonu. Virtuální počítač Standard_GS5 může například dosáhnout maximálně 80 000 IOPS. Podrobné informace o maximálních hodnotách IOPS u virtuálních počítačů najdete v článku o [velikostech virtuálních počítačů s Windows](../virtual-machines/windows/sizes.md).


## <a name="create-and-attach-disks"></a>Vytvoření a připojení disků
Disky můžete vytvořit a připojit při vytváření škálovací sady nebo u existující škálovací sady.

### <a name="attach-disks-at-scale-set-creation"></a>Připojení disků při vytváření škálovací sady
Vytvořte škálovací sadu virtuálních počítačů pomocí rutiny [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Po zobrazení výzvy zadejte uživatelské jméno a heslo pro instance virtuálních počítačů. Za účelem distribuce provozu do jednotlivých instancí virtuálních počítačů se vytvoří také nástroj pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení obsahuje pravidla pro distribuci provozu na portu TCP 80, stejně jako provozu vzdálené plochy na portu TCP 3389 a vzdálené komunikace PowerShellu na portu TCP 5985.

Pomocí parametru `-DataDiskSizeGb` se vytvoří dva disky. První disk má velikost *64* GB a druhý disk *128* GB:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -DataDiskSizeGb 64,128
```

Vytvoření a konfigurace všech prostředků škálovací sady a instancí virtuálních počítačů trvá několik minut.

### <a name="attach-a-disk-to-existing-scale-set"></a>Připojení disku k existující škálovací sadě
Disky můžete připojit také k existující škálovací sadě. Použijte škálovací sadu vytvořenou v předchozím kroku a přidejte další disk pomocí rutiny [Add-AzureRmVmssDataDisk](/powershell/module/azurerm.compute/add-azurermvmssdatadisk). Následující příklad připojí k existující škálovací sadě další *128*GB disk:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzureRmVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Attach a 128 GB data disk to LUN 2
Add-AzureRmVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -CreateOption Empty `
  -Lun 2 `
  -DiskSizeGB 128

# Update the scale set to apply the change
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="prepare-the-data-disks"></a>Příprava datových disků
Disky, které se vytvoří a připojí k instancím virtuálních počítačů ve škálovací sadě, jsou holé disky. Než je budete moct použít pro svá data a aplikace, je potřeba disky připravit. Disky připravíte tak, že vytvoříte oddíl a systém souborů a připojíte je.

K automatizaci tohoto procesu napříč několika instancemi virtuálních počítačů ve škálovací sadě můžete použít rozšíření vlastních skriptů Azure. Toto rozšíření může na jednotlivých instancích virtuálních počítačů místně spouštět skripty, například pro přípravu připojených datových disků. Další informace najdete v tématu [Přehled rozšíření vlastních skriptů](../virtual-machines/windows/extensions-customscript.md).

Následující příklad na každé instanci virtuálního počítače pomocí rutiny [Add-AzureRmVmssExtension](/powershell/module/AzureRM.Compute/Add-AzureRmVmssExtension) spustí skript z ukázkového úložiště GitHub, který připraví všechny připojené holé datové disky:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzureRmVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Define the script for your Custom Script Extension to run
$publicSettings = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
}

# Use Custom Script Extension to prepare the attached data disks
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Pokud chcete potvrdit, že se disky správně připravily, připojte se přes RDP k některé z instancí virtuálních počítačů. 

Nejprve získejte objekt nástroje pro vyrovnávání zatížení pomocí rutiny [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Pak zobrazte pravidla příchozího překladu adres pomocí rutiny [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig). V seznamu pravidel překladu adres bude u jednotlivých instancí virtuálních počítačů uvedený port *FrontendPort*, na kterém naslouchá protokol RDP. Nakonec získejte veřejnou IP adresu nástroje pro vyrovnávání zatížení pomocí rutiny [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress):

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort

# View the public IP address of the load balancer
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIPAddress | Select IpAddress
```

Pokud se chcete připojit k virtuálnímu počítači, zadejte vlastní veřejnou IP adresu a číslo portu požadované instance virtuálního počítače uvedené ve výstupech předchozích příkazů. Po zobrazení výzvy zadejte přihlašovací údaje, které jste použili při vytváření škálovací sady. Pokud používáte Azure Cloud Shell, proveďte tento krok z příkazového řádku místního PowerShellu nebo klienta Vzdálené plochy. Následující příklad se připojí k instanci virtuálního počítače *1*:

```powershell
mstsc /v 52.168.121.216:50001
```

Otevřete na instanci virtuálního počítače místní relaci PowerShellu a zobrazte připojené disky pomocí rutiny [Get-Disk](/powershell/module/storage/get-disk):

```powershell
Get-Disk
```

Následující příklad výstupu ukazuje, že jsou k instanci virtuálního počítače připojené tři datové disky.

```powershell
Number  Friendly Name      HealthStatus  OperationalStatus  Total Size  Partition Style
------  -------------      ------------  -----------------  ----------  ---------------
0       Virtual HD         Healthy       Online                 127 GB  MBR
1       Virtual HD         Healthy       Online                  14 GB  MBR
2       Msft Virtual Disk  Healthy       Online                  64 GB  MBR
3       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
4       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
```

Následujícím způsobem prozkoumejte systémy souborů a přípojné body na instanci virtuálního počítače:

```powershell
Get-Partition
```

Následující příklad výstupu ukazuje, že ke třem datovým diskům jsou přiřazená písmena jednotek:

```powershell
   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000001

PartitionNumber  DriveLetter  Offset   Size  Type
---------------  -----------  ------   ----  ----
1                F            1048576  64 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000002

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                G            1048576  128 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000003

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                H            1048576  128 GB  IFS
```

Disky na jednotlivých instancích virtuálních počítačů ve škálovací sadě se automaticky připraví stejným způsobem. Při případném vertikálním navýšení kapacity škálovací sady se požadované datové disky připojí k novým instancím virtuálních počítačů. Spustí se také rozšíření vlastních skriptů, které disky automaticky připraví.

Ukončete relaci připojení ke vzdálené ploše instance virtuálního počítače.


## <a name="list-attached-disks"></a>Výpis připojených disků
Pokud chcete zobrazit informace o discích připojených ke škálovací sadě, použijte rutinu [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) následujícím způsobem:

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
```

Ve vlastnosti *VirtualMachineProfile.StorageProfile* se zobrazí seznam *datových disků*. Zobrazí se informace o velikosti disku, úrovni úložiště a logické jednotce (LUN). Následující příklad výstupu ukazuje podrobnosti o třech datových discích připojených ke škálovací sadě:

```powershell
DataDisks[0]                            :
  Lun                                   : 0
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 64
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[1]                            :
  Lun                                   : 1
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[2]                            :
  Lun                                   : 2
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
```


## <a name="detach-a-disk"></a>Odpojení disku
Pokud už daný disk nepotřebujete, můžete ho od škálovací sady odpojit. Disk se odebere ze všech instancí virtuálních počítačů ve škálovací sadě. K odpojení disku od škálovací sady použijte rutinu [Remove-AzureRmVmssDataDisk](/powershell/module/azurerm.compute/remove-azurermvmssdatadisk) a zadejte logickou jednotku (LUN) disku. Logické jednotky (LUN) se zobrazí ve výstupu rutiny [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) z předchozí části. Následující příklad odpojí od škálovací sady logickou jednotku (LUN) *3*:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzureRmVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Detach a disk from the scale set
Remove-AzureRmVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -Lun 2

# Update the scale set and detach the disk from the VM instances
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete odebrat škálovací sadu a disky, odstraňte skupinu prostředků a všechny její prostředky pomocí rutiny [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Parametr `-Force` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz. Parametr `-AsJob` vrátí řízení na příkazový řádek bez čekání na dokončení operace.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak vytvořit a používat disky se škálovacími sadami pomocí Azure PowerShellu:

> [!div class="checklist"]
> * Disky s operačním systémem a dočasné disky
> * Datové disky
> * Disky Standard a Premium
> * Výkon disků
> * Připojení a příprava datových disků

V dalším kurzu se dozvíte, jak pro instance virtuálních počítačů ve škálovací sadě použít vlastní image.

> [!div class="nextstepaction"]
> [Použití vlastní image pro instance virtuálních počítačů ve škálovací sadě](tutorial-use-custom-image-powershell.md)
