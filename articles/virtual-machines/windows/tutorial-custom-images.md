---
title: Vytvoření vlastní Image virtuálních počítačů pomocí prostředí Azure PowerShell | Microsoft Docs
description: Kurz – vytvoření vlastní image virtuálního počítače pomocí prostředí Azure PowerShell.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 443f47b98ea063c6fe1f0b3517c00b6cf3692161
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>Vytvořit vlastní image virtuálního počítače Azure pomocí prostředí PowerShell

Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image se dají použít ke spouštění konfigurací, jako jsou předběžné načítání aplikací, konfigurace aplikací a další konfigurace operačního systému. V tomto kurzu vytvoříte vlastní image virtuálního počítače Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nástroj Sysprep a generalize virtuální počítače
> * Vytvoření vlastní image
> * Vytvoření virtuálního počítače z vlastní image
> * Výpis všech imagí v předplatném
> * Odstranění image


## <a name="before-you-begin"></a>Než začnete

Následující postup podrobně popisuje přeměnu existujícího virtuálního počítače na opětovně použitelnou vlastní image, pomocí které můžete vytvářet nové instance virtuálních počítačů.

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač. V případě potřeby si ho můžete nechat vytvořit pomocí tohoto [ukázkového skriptu](../scripts/virtual-machines-windows-powershell-sample-create-vm.md). V průběhu kurzu nahraďte na příslušných místech názvy skupiny prostředků a virtuálních počítačů.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud si zvolíte instalaci a použití prostředí PowerShell místně, tento kurz vyžaduje AzureRM verze modulu 5.6.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="prepare-vm"></a>Příprava virtuálního počítače

Chcete-li vytvořit bitovou kopii virtuálního počítače, je nutné připravit virtuální počítač generalizací virtuální počítač, rušení přidělení a pak označením zdrojového virtuálního počítače jako zobecněn v Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalize virtuální počítač s Windows pomocí nástroje Sysprep

Nástroj Sysprep odstraní všechny vaše osobní informace o účtu, mimo jiné a připraví počítač, který se má použít jako obrázek. Podrobnosti o nástroji Sysprep najdete v tématu [postup použití nástroje Sysprep: Úvod](http://technet.microsoft.com/library/bb457073.aspx).


1. Připojte k virtuálnímu počítači.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na *%windir%\system32\sysprep*a poté spusťte *sysprep.exe*.
3. V **nástroj pro přípravu systému** dialogové okno, vyberte *prostředí Out-of-Box zadejte systému (při prvním zapnutí)*a ujistěte se, že *generalizace* je zaškrtnuté políčko.
4. V **možnosti vypnutí**, vyberte *vypnutí* a pak klikněte na **OK**.
5. Po dokončení nástroj Sysprep vypne virtuální počítač. **Virtuální počítač nerestartuje**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Uvolnění virtuálního počítače a jeho označení za generalizovaný

Virtuální počítač na vytvoření bitové kopie, je třeba navrácena a označené jako zobecněn v Azure.

Virtuální počítač pomocí navrátil [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Nastavte stav virtuálního počítače na `-Generalized` pomocí [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```azurepowershell-interactive
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Vytvoření image

Nyní můžete vytvořit image virtuálního počítače pomocí [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) a [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). Následující příklad vytvoří image *myImage* z virtuálního počítače *myVM*.

Získáte virtuální počítač. 

```azurepowershell-interactive
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Vytvořte konfiguraci bitové kopie.

```azurepowershell-interactive
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Vytvoření bitové kopie.

```azurepowershell-interactive
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Vytvoření virtuálních počítačů z image

Teď, když máte image, můžete vytvořit jeden nebo více nové virtuální počítače z image. Vytvoření virtuálního počítače z vlastní image je velmi podobný k vytvoření virtuálního počítače pomocí image pořízenou prostřednictvím Marketplace. Pokud používáte image pořízenou prostřednictvím Marketplace, je nutné zadat informace o bitové kopie, zprostředkovatele bitové kopie, nabídky, SKU a verzi. Pomocí parametru zjednodušené nastavit pro [New-AzureRMVM]() rutiny, stačí zadat název vlastní image, dokud je ve stejné skupině prostředků. 

Tento příklad vytvoří virtuální počítač s názvem *myVMfromImage* z *myImage*v *myResourceGroup*.


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

Zde jsou některé příklady běžné úlohy správy bitové kopie a postup jejich dokončení pomocí prostředí PowerShell.

Zobrazí seznam všech Image podle názvu.

```azurepowershell-interactive
$images = Find-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Odstranění image. Tento příklad odstraní image *myOldImage* ze skupiny prostředků *myResourceGroup*.

```azurepowershell-interactive
Remove-AzureRmImage `
    -ImageName myOldImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili vlastní image virtuálního počítače. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Nástroj Sysprep a generalize virtuální počítače
> * Vytvoření vlastní image
> * Vytvoření virtuálního počítače z vlastní image
> * Výpis všech imagí v předplatném
> * Odstranění image

Přechodu na další informace o tom, jak vysoce dostupné virtuální počítače v dalším kurzu.

> [!div class="nextstepaction"]
> [Vytvoření vysoce dostupných virtuálních počítačů](tutorial-availability-sets.md)



