---
title: Kurz vytváření vlastních imagí virtuálních počítačů pomocí Azure PowerShellu | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí Azure PowerShellu vytvořit a spravovat vlastní image virtuálního počítače v Azure.
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
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 192ecf0cf4f97a709808fa04f676035e8a672b79
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55976942"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Kurz: Vytvoření vlastní image virtuálního počítače Azure pomocí Azure Powershellu

Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní Image je možné spustit nasazení a zajistit konzistenci napříč několika virtuálními počítači. V tomto kurzu vytvoříte vlastní image virtuálního počítače Azure pomocí Powershellu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nástroj Sysprep a generalizace virtuálních počítačů
> * Vytvoření vlastní image
> * Vytvoření virtuálního počítače z vlastní image
> * Výpis všech imagí v předplatném
> * Odstranění image

## <a name="before-you-begin"></a>Před zahájením

Následující postup podrobně popisuje přeměnu existujícího virtuálního počítače na opětovně použitelnou vlastní image, pomocí které můžete vytvářet nové instance virtuálních počítačů.

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač. V případě potřeby si ho můžete nechat vytvořit pomocí tohoto [ukázkového skriptu](../scripts/virtual-machines-windows-powershell-sample-create-vm.md). V průběhu kurzu nahraďte na příslušných místech názvy skupiny prostředků a virtuálních počítačů.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="prepare-vm"></a>Příprava virtuálního počítače

Pokud chcete vytvořit image virtuálního počítače, budete muset připravit zdrojový virtuální počítač tak, že ji zobecníte, uvolníte ho a potom označení za generalizovaný s Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizace virtuálního počítače s Windows pomocí nástroje Sysprep

Nástroj Sysprep kromě jiného odebere všechny informace o vašich osobních účtech a připraví počítač, aby se dal použít jako image. Podrobnosti o nástroji Sysprep najdete v tématu [použití nástroje Sysprep: Úvod](https://technet.microsoft.com/library/bb457073.aspx).


1. Připojte se k virtuálnímu počítači.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na *%windir%\system32\sysprep*a pak spusťte `sysprep.exe`.
3. V dialogovém okně **Nástroj pro přípravu systému** vyberte **Zobrazit prostředí prvního spuštění počítače** a ujistěte se, že je zaškrtnuté políčko **Generalizovat**.
4. V části **Možnosti vypnutí** vyberte **Vypnout** a potom klikněte na **OK**.
5. Po dokončení nástroj Sysprep vypne virtuální počítač. **Virtuální počítač nerestartujte**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Uvolnění virtuálního počítače a jeho označení za generalizovaný

Chcete-li vytvořit image, musí být virtuální počítač uvolněný a označený jako generalizovaný v Azure.

Přidělení virtuálního počítače pomocí [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

Nastavte stav virtuálního počítače na `-Generalized` pomocí [Set-AzVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>Vytvoření image

Nyní můžete vytvořit image virtuálního počítače pomocí [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) a [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage). Následující příklad vytvoří image *myImage* z virtuálního počítače *myVM*.

Získejte virtuální počítač. 

```azurepowershell-interactive
$vm = Get-AzVM `
   -Name myVM `
   -ResourceGroupName myResourceGroup
```

Vytvořte konfiguraci image.

```azurepowershell-interactive
$image = New-AzImageConfig `
   -Location EastUS `
   -SourceVirtualMachineId $vm.ID 
```

Vytvořte image.

```azurepowershell-interactive
New-AzImage `
   -Image $image `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Vytvoření virtuálních počítačů z image

Když teď máte image, můžete z ní vytvořit jeden nebo více nových virtuálních počítačů. Vytvoření virtuálního počítače z vlastní image je podobné vytvoření virtuálního počítače pomocí image pořízené na Marketplace. Při použití image pořízené na Marketplace je nutné zadat informace o image, poskytovateli image, nabídce, SKU a verzi. Pomocí parametru zjednodušené nastavit pro [rutiny New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) rutiny, stačí zadat název vlastní image, dokud je ve stejné skupině prostředků. 

Tento příklad vytvoří virtuální počítač s názvem *myVMfromImage* z *myImage* obrázků v *myResourceGroup*.


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

## <a name="image-management"></a>Správa imagí 

Tady je několik příkladů běžných úloh se spravovanými imagemi a postup pro jejich provedení pomocí PowerShellu.

Zobrazení seznamu všech imagí podle názvu.

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

Odstranění image. Tento příklad odstraní image s názvem *myImage* z *myResourceGroup*.

```azurepowershell-interactive
Remove-AzImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili vlastní image virtuálního počítače. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Nástroj Sysprep a generalizace virtuálních počítačů
> * Vytvoření vlastní image
> * Vytvoření virtuálního počítače z vlastní image
> * Výpis všech imagí v předplatném
> * Odstranění image

Přejděte k dalšímu kurzu, kde se seznámíte s virtuálními počítači s vysokou dostupností.

> [!div class="nextstepaction"]
> [Vytvoření vysoce dostupných virtuálních počítačů](tutorial-availability-sets.md)



