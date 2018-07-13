---
title: Kurz správy disků v Azure pomocí Azure PowerShellu | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí Azure PowerShellu vytvářet a spravovat disky Azure pro virtuální počítače.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 67e95ea4b816e1c21b5a43233360c7fa89f43a7a
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929300"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Kurz: Správa disků v Azure pomocí Azure PowerShellu

Virtuální počítače Azure využívají disky k ukládání svých operačních systémů, aplikací a dat. Při vytváření virtuálního počítače je důležité, abyste zvolili vhodnou velikost disku a konfiguraci pro očekávané úlohy. Tento kurz se zaměřuje na nasazení a správu disků virtuálních počítačů. Dozvíte se o těchto tématech:

> [!div class="checklist"]
> * Disky s operačním systémem a dočasné disky
> * Datové disky
> * Disky Standard a Premium
> * Výkon disků
> * Připojení a příprava datových disků

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte v tomto kurzu použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="default-azure-disks"></a>Výchozí disky v Azure

Při vytvoření virtuálního počítače Azure se k němu automaticky připojí dva disky. 

**Disk s operačním systémem:** Disky s operačním systémem můžou mít velikost až 4 terabajty a hostují operační systém virtuálního počítače.  Disku s operačním systémem je ve výchozím nastavení přiřazeno písmeno jednotky *c*. Konfigurace ukládání do mezipaměti na disku je u disku s operačním systémem optimalizovaná s ohledem na výkon operačního systému. Disk s operačním systémem **by neměl** hostit aplikace nebo data. Pro aplikace a data použijte datový disk, který je podrobněji popsán dále v tomto článku.

**Dočasný disk:** Dočasné disky používají jednotku SSD, která je umístěná na stejném hostiteli Azure jako virtuální počítač. Dočasné disky mají vysoký výkon a můžou se používat pro operace, jako je zpracování dočasných dat. V případě přesunutí virtuálního počítače na nového hostitele se ale všechna data uložená na dočasném disku odeberou. Velikost dočasného disku se určuje podle velikosti virtuálního počítače. Dočasným diskům se ve výchozím nastavení přiřazuje písmeno jednotky *d*.

### <a name="temporary-disk-sizes"></a>Velikosti dočasného disku

| Typ | Běžné velikosti | Maximální velikost dočasného disku (GiB) |
|----|----|----|
| [Obecné účely](sizes-general.md) | Řady A, B a D | 1600 |
| [Optimalizované z hlediska výpočetních služeb](sizes-compute.md) | Řada F | 576 |
| [Optimalizované z hlediska paměti](sizes-memory.md) | Řady D, E, G a M | 6144 |
| [Optimalizované z hlediska úložiště](sizes-storage.md) | Řada L | 5630 |
| [GPU](sizes-gpu.md) | Řada N | 1440 |
| [Vysoký výkon](sizes-hpc.md) | Řady A a H | 2000 |

## <a name="azure-data-disks"></a>Datové disky Azure

Pro instalaci aplikací a ukládání dat je možné přidat další datové disky. Datové disky by se měly používat v každé situaci, kdy se vyžaduje odolné a responzivní úložiště dat. Každý datový disk má maximální kapacitu 4 terabajty. Velikost virtuálního počítače určuje, kolik datových disků se k němu může připojit. Na každý virtuální procesor virtuálního počítače je možné připojit dva datové disky. 

### <a name="max-data-disks-per-vm"></a>Maximum datových disků na virtuální počítač

| Typ | Běžné velikosti | Maximum datových disků na virtuální počítač |
|----|----|----|
| [Obecné účely](sizes-general.md) | Řady A, B a D | 64 |
| [Optimalizované z hlediska výpočetních služeb](sizes-compute.md) | Řada F | 64 |
| [Optimalizované z hlediska paměti](sizes-memory.md) | Řady D, E, G a M | 64 |
| [Optimalizované z hlediska úložiště](sizes-storage.md) | Řada L | 64 |
| [GPU](sizes-gpu.md) | Řada N | 64 |
| [Vysoký výkon](sizes-hpc.md) | Řady A a H | 64 |

## <a name="vm-disk-types"></a>Typy disků virtuálního počítače

Azure poskytuje dva typy disků.

### <a name="standard-disk"></a>Disk Standard

Služba Storage úrovně Standard je založená na jednotkách HDD a poskytuje nákladově efektivní úložiště se zachováním výkonu. Disky Standard jsou ideální pro nákladově efektivní vývoj a testování.

### <a name="premium-disk"></a>Disk Premium

Disky Premium jsou založené na vysoce výkonných discích SSD s nízkou latencí. Jsou ideální pro virtuální počítače s produkčními úlohami. Služba Premium Storage podporuje virtuální počítače řad DS, DSv2, GS a FS. Disky Premium se dělí na pět typů (P10, P20, P30, P40, P50) podle své velikosti. Při výběru se hodnota velikosti disku zaokrouhluje nahoru na nejbližší typ. Pokud je například velikost menší než 128 GB, jedná se o typ disku P10, nebo pokud je mezi 129 GB a 512 GB, je to disk P20.

### <a name="premium-disk-performance"></a>Výkon disků Premium

|Typ disku pro Premium Storage | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Velikost disku (zaokrouhluje se nahoru) | 32 GB | 64 GB | 128 GB | 512 GB | 1 024 GB (1 TB) | 2 048 GB (2 TB) | 4 095 GB (4 TB) |
| Maximum vstupně-výstupních operací za sekundu (IOPS) na disk | 120 | 240 | 500 | 2 300 | 5 000 | 7 500 | 7 500 |
Propustnost / disk | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

V tabulce výše se sice uvádí maximum vstupně-výstupních operací za sekundu (IOPS), ale prokládáním více datových disků je možné dosáhnout i vyšší úrovně výkonu. Například k virtuálnímu počítači Standard_GS5 je možné připojit 64 datových disků. Pokud je velikost každého z těchto disků P30, můžete dosáhnout maximální hodnoty 80 000 IOPS. Podrobné informace o maximálních hodnotách IOPS u virtuálních počítačů najdete v článku o [velikostech a typech virtuálních počítačů](./sizes.md).

## <a name="create-and-attach-disks"></a>Vytvoření a připojení disků

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač. V případě potřeby vytvořte virtuální počítač pomocí následujících příkazů.

Uživatelské jméno a heslo potřebné pro účet správce na virtuálním počítači můžete nastavit pomocí příkazu [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Vytvořte virtuální počítač pomocí příkazu [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -AsJob
```

Parametr `-AsJob` vytvoří virtuální počítač jako úlohu na pozadí, takže budete mít k dispozici příkazový řádek PowerShellu. Podrobnosti úloh na pozadí můžete zobrazit pomocí rutiny `Job`.

Vytvořte počáteční konfiguraci pomocí rutiny [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig). Následující příklad nakonfiguruje disk o velikosti 128 GB.

```azurepowershell-interactive
$diskConfig = New-AzureRmDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Vytvořte datový disk pomocí příkazu [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk).

```azurepowershell-interactive
$dataDisk = New-AzureRmDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Získejte virtuální počítač, ke kterému chcete přidat datový disk, pomocí příkazu [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm).

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Přidejte datový disk do konfigurace virtuálního počítače pomocí příkazu [Add-AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
$vm = Add-AzureRmVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Aktualizujte virtuální počítač pomocí příkazu [Update-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
Update-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Příprava datových disků

Po připojení disku k virtuálnímu počítači je třeba nakonfigurovat operační systém tak, aby mohl disk používat. Následující příklad ukazuje postup při ruční konfiguraci prvního disku přidaného do virtuálního počítače. Tento proces je také možné automatizovat pomocí [rozšíření vlastních skriptů](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Ruční konfigurace

Vytvořte připojení RDP k virtuálnímu počítači. Otevřete PowerShell a spusťte tento skript.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' | `
Initialize-Disk -PartitionStyle MBR -PassThru | `
New-Partition -AssignDriveLetter -UseMaximumSize | `
Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s disky virtuálních počítačů, jako jsou:

> [!div class="checklist"]
> * Disky s operačním systémem a dočasné disky
> * Datové disky
> * Disky Standard a Premium
> * Výkon disků
> * Připojení a příprava datových disků

V dalším kurzu se dozvíte, jak automatizovat konfiguraci virtuálních počítačů.

> [!div class="nextstepaction"]
> [Automatizace konfigurace virtuálních počítačů](./tutorial-automate-vm-deployment.md)
