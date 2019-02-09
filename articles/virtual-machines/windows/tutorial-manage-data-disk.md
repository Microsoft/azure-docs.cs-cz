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
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.subservice: disks
ms.openlocfilehash: 50470edff81194b9c8885aa94d1eab1e6c18ad88
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984082"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Kurz: Správa disků v Azure pomocí Azure PowerShellu

Virtuální počítače Azure využívají disky k ukládání svých operačních systémů, aplikací a dat. Při vytváření virtuálního počítače je důležité, abyste zvolili vhodnou velikost a konfiguraci disku pro očekávanou pracovní zátěž. Tento kurz se zaměřuje na nasazení a správu disků virtuálních počítačů. Dozvíte se o těchto tématech:

> [!div class="checklist"]
> * Disky s operačním systémem a dočasné disky
> * Datové disky
> * Disky Standard a Premium
> * Výkon disků
> * Připojení a příprava datových disků

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="default-azure-disks"></a>Výchozí disky v Azure

Při vytvoření virtuálního počítače Azure se k němu automaticky připojí dva disky. 

**Disk s operačním systémem:** Disky s operačním systémem můžou mít velikost až 4 terabajty a hostují operační systém virtuálního počítače.  Disku s operačním systémem je ve výchozím nastavení přiřazené písmeno jednotky *C*. Konfigurace ukládání do mezipaměti na disku je u disku s operačním systémem optimalizovaná s ohledem na výkon operačního systému. Disk s operačním systémem **by neměl** hostit aplikace nebo data. Pro aplikace a data použijte datový disk, který je podrobněji popsán dále v tomto článku.

**Dočasný disk:** Dočasné disky používají jednotku SSD, která je umístěná na stejném hostiteli Azure jako virtuální počítač. Dočasné disky mají vysoký výkon a můžou se používat pro operace, jako je zpracování dočasných dat. V případě přesunutí virtuálního počítače na nového hostitele se ale všechna data uložená na dočasném disku odeberou. Velikost dočasného disku se určuje podle [velikosti virtuálního počítače](sizes.md). Dočasným diskům se ve výchozím nastavení přiřazuje písmeno jednotky *D*.



## <a name="azure-data-disks"></a>Datové disky Azure

Pro instalaci aplikací a ukládání dat je možné přidat další datové disky. Datové disky by se měly používat v každé situaci, kdy se vyžaduje odolné a responzivní úložiště dat. Každý datový disk má maximální kapacitu 4 terabajty. Velikost virtuálního počítače určuje, kolik datových disků se k němu může připojit. Na každý virtuální procesor virtuálního počítače je možné připojit čtyři datové disky. 


## <a name="vm-disk-types"></a>Typy disků virtuálního počítače

Azure poskytuje dva typy disků.

**Disky Standard:** využívají pevné disky a poskytují nákladově efektivní úložiště se zachováním výkonu. Disky Standard jsou ideální pro nákladově efektivní vývoj a testování.

**Disky Premium:** využívají vysoce výkonné disky SSD s nízkou latencí. Jsou ideální pro virtuální počítače s produkčními úlohami. Služba Premium Storage podporuje virtuální počítače řad DS, DSv2, GS a FS. Disky Premium se dělí na pět typů (P10, P20, P30, P40, P50) podle své velikosti. Při výběru se hodnota velikosti disku zaokrouhluje nahoru na nejbližší typ. Pokud je například velikost menší než 128 GB, jedná se o typ disku P10, nebo pokud je mezi 129 GB a 512 GB, je to disk P20.

### <a name="premium-disk-performance"></a>Výkon disků Premium

|Typ disku pro Premium Storage | P4 | P6 | P10 | P20 | P30 | P40 | P50 | P60 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Velikost disku (zaokrouhluje se nahoru) | 32 GiB | 64 GiB | 128 GiB | 512 GiB | 1 024 GiB (1 TiB) | 2 048 GiB (2 TiB) | 4 095 GiB (4 TiB) | 8 192 GiB (8 TiB)
| Maximum vstupně-výstupních operací za sekundu (IOPS) na disk | 120 | 240 | 500 | 2 300 | 5 000 | 7 500 | 7 500 | 12 500 |
Propustnost / disk | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s | 480 MB/s |

V tabulce výše se sice uvádí maximum vstupně-výstupních operací za sekundu (IOPS), ale prokládáním více datových disků je možné dosáhnout i vyšší úrovně výkonu. Například k virtuálnímu počítači Standard_GS5 je možné připojit 64 datových disků. Pokud je velikost každého z těchto disků P30, můžete dosáhnout maximální hodnoty 80 000 IOPS. Podrobné informace o maximálních hodnotách IOPS u virtuálních počítačů najdete v článku o [velikostech a typech virtuálních počítačů](./sizes.md).

## <a name="create-and-attach-disks"></a>Vytvoření a připojení disků

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač. V případě potřeby vytvořte virtuální počítač pomocí následujících příkazů.

Uživatelské jméno a heslo potřebné pro účet správce na virtuálním počítači můžete nastavit pomocí příkazu [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):


Vytvoření virtuálního počítače s [rutiny New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Zobrazí se výzva k zadání uživatelského jména a hesla pro účet správce virtuálního počítače.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


Vytvořte počáteční konfiguraci pomocí [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig). Následující příklad nakonfiguruje disk o velikosti 128 GB.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Vytvořit datový disk se [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk) příkazu.

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Převeďte virtuální počítač, který chcete přidat datový disk, pomocí [rutiny Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) příkazu.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Přidání datového disku do konfigurace virtuálního počítače s [přidat AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/add-azvmdatadisk) příkazu.

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Aktualizovat virtuální počítač s [rutiny Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/add-azvmdatadisk) příkazu.

```azurepowershell-interactive
Update-AzVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Příprava datových disků

Po připojení disku k virtuálnímu počítači je třeba nakonfigurovat operační systém tak, aby mohl disk používat. Následující příklad ukazuje postup při ruční konfiguraci prvního disku přidaného do virtuálního počítače. Tento proces je také možné automatizovat pomocí [rozšíření vlastních skriptů](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Ruční konfigurace

Vytvořte připojení RDP k virtuálnímu počítači. Otevřete PowerShell a spusťte tento skript.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>Ověření datového disku

Pokud chcete ověřit připojení datového disku, podívejte se na profil úložiště (`StorageProfile`) připojených datových disků (`DataDisks`).

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

Výstup by měl vypadat přibližně jako v tomto příkladu:

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>Další postup

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
