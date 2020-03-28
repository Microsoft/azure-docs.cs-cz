---
title: Kurz – vytváření vlastních ibi virtuálních počítačů pomocí Azure PowerShellu
description: V tomto kurzu zjistíte, jak pomocí Azure PowerShellu vytvořit a spravovat vlastní image virtuálního počítače v Azure.
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7360798f2f95184145a856babf501e3080cbaaf4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76274191"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Kurz: Vytvoření vlastní image virtuálního počítače Azure pomocí Azure PowerShellu

Vlastní image jsou podobné imagím z marketplace, ale vytváříte je sami. Vlastní image lze použít k nasazení nasazení a zajištění konzistence mezi více virtuálními počítači. V tomto kurzu vytvoříte vlastní image virtuálního počítače Azure pomocí PowerShellu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nástroj Sysprep a generalizace virtuálních počítačů
> * Vytvoření vlastní image
> * Vytvoření virtuálního počítače z vlastní image
> * Výpis všech imagí v předplatném
> * Odstranění image

Ve verzi Public Preview máme službu [Azure VM Image Builder.](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview) Jednoduše popište vlastní nastavení v šabloně a zvládne kroky vytváření obrázků v tomto článku. [Vyzkoušejte Azure Image Builder (preview).](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder)

## <a name="before-you-begin"></a>Než začnete

Následující postup podrobně popisuje přeměnu existujícího virtuálního počítače na opětovně použitelnou vlastní image, pomocí které můžete vytvářet nové instance virtuálních počítačů.

K dokončení příkladu v tomto kurzu potřebujete existující virtuální počítač. V případě potřeby může tato [ukázka skriptu](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) vytvořit jeden pro vás. V průběhu kurzu nahraďte na příslušných místech názvy skupiny prostředků a virtuálních počítačů.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné [https://shell.azure.com/powershell](https://shell.azure.com/powershell)kartě prohlížeče tak, že přejdete na . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="prepare-vm"></a>Příprava virtuálního počítače

Chcete-li vytvořit image virtuálního počítače, musíte připravit zdrojový virtuální počítač generalizací, zrušením přidělení a následným označením jako zobecněný pomocí Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizace virtuálního počítače s Windows pomocí nástroje Sysprep

Nástroj Sysprep kromě jiného odebere všechny informace o vašich osobních účtech a připraví počítač, aby se dal použít jako image. Další informace o nástroji Sysprep najdete v článku věnovaném [úvodu do použití nástroje Sysprep](https://technet.microsoft.com/library/bb457073.aspx).


1. Připojte se k virtuálnímu počítači.
2. Otevřete okno příkazového řádku jako správce. Změňte adresář na *%windir%\system32\sysprep* `sysprep.exe`a spusťte program .
3. V dialogovém okně **Nástroj pro přípravu systému** vyberte **Zobrazit prostředí prvního spuštění počítače** a ujistěte se, že je zaškrtnuté políčko **Generalizovat**.
4. V části **Možnosti vypnutí** vyberte **Vypnout** a potom klikněte na **OK**.
5. Po dokončení nástroj Sysprep vypne virtuální počítač. **Virtuální počítač nerestartujte**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Uvolnění virtuálního počítače a jeho označení za generalizovaný

Chcete-li vytvořit image, musí být virtuální počítač uvolněný a označený jako generalizovaný v Azure.

Navrátit virtuální ho pomocí [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

Nastavte stav virtuálního počítače `-Generalized` na [použití Set-AzVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>Vytvoření image

Nyní můžete vytvořit obrázek virtuálního virtuálního virtuálního mísy pomocí [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) a [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage). Následující příklad vytvoří image *myImage* z virtuálního počítače *myVM*.

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

Když teď máte image, můžete z ní vytvořit jeden nebo více nových virtuálních počítačů. Vytvoření virtuálního počítače z vlastní image je podobné vytvoření virtuálního počítače pomocí image pořízené na Marketplace. Při použití image pořízené na Marketplace je nutné zadat informace o image, poskytovateli image, nabídce, SKU a verzi. Pomocí zjednodušené sady parametrů pro rutinu [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) stačí zadat název vlastní bitové kopie, pokud je ve stejné skupině prostředků. 

Tento příklad vytvoří virtuální ho disponuje s názvem *myVMfromImage* z image *myImage* v *myResourceGroup*.


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

Doporučujeme omezit počet souběžných nasazení na 20 virtuálních počítače z jedné bitové kopie. Pokud plánujete rozsáhlé souběžné nasazení více než 20 virtuálních počítačů ze stejné vlastní image, měli byste použít [Galerii sdílených bitových obrázků](shared-image-galleries.md) s více replikami bitových obrázků. 


## <a name="image-management"></a>Správa imagí 

Tady je několik příkladů běžných úloh se spravovanými imagemi a postup pro jejich provedení pomocí PowerShellu.

Zobrazení seznamu všech imagí podle názvu.

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

Odstranění image. Tento příklad odstraní obrázek s názvem *myImage* z *myResourceGroup*.

```azurepowershell-interactive
Remove-AzImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili vlastní image virtuálního počítače. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Nástroj Sysprep a generalizace virtuálních počítačů
> * Vytvoření vlastní image
> * Vytvoření virtuálního počítače z vlastní image
> * Výpis všech imagí v předplatném
> * Odstranění image

Přejdete k dalšímu kurzu, kde se dozvíte, jak vytvořit vysoce dostupné virtuální počítače.

> [!div class="nextstepaction"]
> [Vytvoření vysoce dostupných virtuálních počítačů](tutorial-availability-sets.md)



