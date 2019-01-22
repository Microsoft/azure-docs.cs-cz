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
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f2372382f36ba5a4ad801b4781bc7e04fa38694b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437602"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Kurz: Vytvoření vlastní image virtuálního počítače Azure pomocí Azure Powershellu

Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. V tomto kurzu vytvoříte vlastní image virtuálního počítače Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nástroj Sysprep a generalizace virtuálních počítačů
> * Vytvoření vlastní image
> * Vytvoření virtuálního počítače z vlastní image
> * Výpis všech imagí v předplatném
> * Odstranění image

## <a name="before-you-begin"></a>Před zahájením

Následující postup podrobně popisuje přeměnu existujícího virtuálního počítače na opětovně použitelnou vlastní image, pomocí které můžete vytvářet nové instance virtuálních počítačů.

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač. V případě potřeby si ho můžete nechat vytvořit pomocí tohoto [ukázkového skriptu](../scripts/virtual-machines-windows-powershell-sample-create-vm.md). V průběhu kurzu nahraďte na příslušných místech názvy skupiny prostředků a virtuálních počítačů.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul AzureRM verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="prepare-vm"></a>Příprava virtuálního počítače

Pokud chcete vytvořit image virtuálního počítače, musíte virtuální počítač připravit tak, že jej generalizujete, uvolníte ho a pak označíte zdrojový virtuální počítač za generalizovaný v Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizace virtuálního počítače s Windows pomocí nástroje Sysprep

Nástroj Sysprep kromě jiného odebere všechny informace o vašich osobních účtech a připraví počítač, aby se dal použít jako image. Podrobnosti o nástroji Sysprep najdete v tématu [použití nástroje Sysprep: Úvod](https://technet.microsoft.com/library/bb457073.aspx).


1. Připojte se k virtuálnímu počítači.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na *%windir%\system32\sysprep* a poté spusťte *sysprep.exe*.
3. V dialogovém okně **Nástroj pro přípravu systému** vyberte *Zobrazit prostředí prvního spuštění počítače* a ujistěte se, že je zaškrtnuté políčko *Generalizovat*.
4. V části **Možnosti vypnutí** vyberte *Vypnout* a potom klikněte na **OK**.
5. Po dokončení nástroj Sysprep vypne virtuální počítač. **Virtuální počítač nerestartujte**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Uvolnění virtuálního počítače a jeho označení za generalizovaný

Chcete-li vytvořit image, musí být virtuální počítač uvolněný a označený jako generalizovaný v Azure.

Uvolněte virtuální počítač pomocí rutiny [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Nastavte stav virtuálního počítače na `-Generalized` pomocí rutiny [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```azurepowershell-interactive
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Vytvoření image

Teď můžete vytvořit image virtuálního počítače pomocí rutin [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) a [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). Následující příklad vytvoří image *myImage* z virtuálního počítače *myVM*.

Získejte virtuální počítač. 

```azurepowershell-interactive
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Vytvořte konfiguraci image.

```azurepowershell-interactive
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Vytvořte image.

```azurepowershell-interactive
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Vytvoření virtuálních počítačů z image

Když teď máte image, můžete z ní vytvořit jeden nebo více nových virtuálních počítačů. Vytvoření virtuálního počítače z vlastní image je podobné vytvoření virtuálního počítače pomocí image pořízené na Marketplace. Při použití image pořízené na Marketplace je nutné zadat informace o image, poskytovateli image, nabídce, SKU a verzi. Při použití zjednodušené sady parametrů pro rutinu [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) vám stačí zadat název vlastní image, dokud je ve stejné skupině prostředků. 

Tento příklad vytvoří virtuální počítač s názvem *myVMfromImage* z image *myImage* ve skupině prostředků *myResourceGroup*.


```azurepowershell-interactive
New-AzureRmVm `
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
$images = Get-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Odstranění image. Tento příklad odstraní image s názvem *myImage* z *myResourceGroup*.

```azurepowershell-interactive
Remove-AzureRmImage `
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



