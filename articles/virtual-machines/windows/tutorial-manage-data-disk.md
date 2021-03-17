---
title: 'Kurz: Správa disků v Azure pomocí Azure PowerShellu'
description: V tomto kurzu zjistíte, jak pomocí Azure PowerShellu vytvářet a spravovat disky Azure pro virtuální počítače.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a61d7425a7a907230008ab1d4f15a836150e1518
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549052"
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

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="default-azure-disks"></a>Výchozí disky v Azure

Při vytvoření virtuálního počítače Azure se k němu automaticky připojí dva disky. 

**Disk s operačním systémem:** Disky s operačním systémem můžou mít velikost až 4 terabajty a hostují operační systém virtuálního počítače. Pokud vytvoříte nový virtuální počítač z bitové kopie [Azure Marketplace](https://azure.microsoft.com/marketplace/) , obvykle 127 GB (ale některé image mají menší velikosti disků s operačním systémem). Disku s operačním systémem je ve výchozím nastavení přiřazené písmeno jednotky *C*. Konfigurace ukládání do mezipaměti na disku je u disku s operačním systémem optimalizovaná s ohledem na výkon operačního systému. Disk s operačním systémem **by neměl** hostit aplikace nebo data. Pro aplikace a data použijte datový disk, který je podrobněji popsán dále v tomto článku.

**Dočasný disk:** Dočasné disky používají jednotku SSD, která je umístěná na stejném hostiteli Azure jako virtuální počítač. Dočasné disky mají vysoký výkon a můžou se používat pro operace, jako je zpracování dočasných dat. V případě přesunutí virtuálního počítače na nového hostitele se ale všechna data uložená na dočasném disku odeberou. Velikost dočasného disku se určuje podle [velikosti virtuálního počítače](../sizes.md). Dočasným diskům se ve výchozím nastavení přiřazuje písmeno jednotky *D*.

## <a name="azure-data-disks"></a>Datové disky Azure

Pro instalaci aplikací a ukládání dat je možné přidat další datové disky. Datové disky by se měly používat v každé situaci, kdy se vyžaduje odolné a responzivní úložiště dat. Velikost virtuálního počítače určuje, kolik datových disků se k němu může připojit.

## <a name="vm-disk-types"></a>Typy disků virtuálního počítače

Azure poskytuje dva typy disků.

**Disky Standard:** využívají pevné disky a poskytují nákladově efektivní úložiště se zachováním výkonu. Disky Standard jsou ideální pro nákladově efektivní vývoj a testování.

**Disky Premium založené na discích** SSD s vysokým výkonem a nízkou latencí. Jsou ideální pro virtuální počítače s produkčními úlohami. Velikosti virtuálních počítačů s názvem  **s** v [názvu velikosti](../vm-naming-conventions.md), obvykle podporují Premium Storage. Například virtuální počítače DS-Series, DSv2-Series, GS-Series a FS-series podporují Prémiové úložiště. Při výběru se hodnota velikosti disku zaokrouhluje nahoru na nejbližší typ. Například pokud je velikost disku větší než 64 GB, ale menší než 128 GB, je typ disku P10. 
<br>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Když zřizujete disk služby Premium Storage na rozdíl od standardního úložiště, zaručujete si kapacitu, IOPS a propustnost tohoto disku. Pokud například vytvoříte P50 disk, Azure zřídí 4 095 GB kapacity úložiště, 7 500 IOPS a propustnost 250 MB/s pro tento disk. Vaše aplikace může využívat celou kapacitu a výkon. SSD úrovně Premium disky jsou navržené tak, aby poskytovaly nízké latence v řádu milisekund a cílové IOPS a propustnost popsané v předchozí tabulce 99,9% času.

V tabulce výše se sice uvádí maximum vstupně-výstupních operací za sekundu (IOPS), ale prokládáním více datových disků je možné dosáhnout i vyšší úrovně výkonu. Například k virtuálnímu počítači Standard_GS5 je možné připojit 64 datových disků. Pokud je velikost každého z těchto disků P30, můžete dosáhnout maximální hodnoty 80 000 IOPS. Podrobné informace o maximálních hodnotách IOPS u virtuálních počítačů najdete v článku o [velikostech a typech virtuálních počítačů](../sizes.md).

## <a name="create-and-attach-disks"></a>Vytvoření a připojení disků

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač. V případě potřeby vytvořte virtuální počítač pomocí následujících příkazů.

Uživatelské jméno a heslo potřebné pro účet správce na virtuálním počítači můžete nastavit pomocí příkazu [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):


Vytvořte virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Zobrazí se výzva k zadání uživatelského jména a hesla pro účet správce virtuálního počítače.

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


Vytvořte počáteční konfiguraci pomocí [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig). Následující příklad nakonfiguruje disk o velikosti 128 GB.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Pomocí příkazu [New-AzDisk](/powershell/module/az.compute/new-azdisk) vytvořte datový disk.

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Pomocí příkazu [Get-AzVM](/powershell/module/az.compute/get-azvm) Získejte virtuální počítač, ke kterému chcete přidat datový disk.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Přidejte datový disk do konfigurace virtuálního počítače pomocí příkazu [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) .

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Aktualizujte virtuální počítač pomocí příkazu [Update-AzVM](/powershell/module/az.compute/add-azvmdatadisk) .

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
