---
title: Vytvoření virtuálního počítače ze spravované image v Azure
description: V modelu nasazení Správce prostředků vytvořte virtuální počítač s Windows z generalizované spravované Image pomocí Azure PowerShell nebo Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: de59edc2e2c702993efd6187a590264d9aac16a7
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841927"
---
# <a name="create-a-vm-from-a-managed-image"></a>Vytvoření virtuálního počítače ze spravované image

Můžete vytvořit několik virtuálních počítačů z image spravovaného virtuálního počítače Azure pomocí Azure Portal nebo PowerShellu. Spravovaná image virtuálního počítače obsahuje informace potřebné k vytvoření virtuálního počítače, včetně operačního systému a datových disků. Virtuální pevné disky (VHD), které tvoří bitovou kopii, včetně disků operačního systému i všech datových disků, jsou uloženy jako spravované disky. 

Před vytvořením nového virtuálního počítače budete muset [vytvořit spravovanou image virtuálního počítače](capture-image-resource.md) , která se použije jako zdrojová image, a udělit všem uživatelům, kteří mají mít přístup k imagi, přístup pro čtení k imagi. 


## <a name="use-the-portal"></a>Použití portálu

1. Pro vyhledání spravované image použijte [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **obrázky**.
3. Ze seznamu vyberte obrázek, který chcete použít. Otevře se stránka s **přehledem** obrázku.
4. V nabídce vyberte **vytvořit virtuální počítač** .
5. Zadejte informace o virtuálním počítači. Uživatelské jméno a heslo, které tady zadáte, se použije k přihlášení k virtuálnímu počítači. Po dokončení vyberte **OK**. Nový virtuální počítač můžete vytvořit v existující skupině prostředků, nebo výběrem možnosti **vytvořit novou** vytvořte novou skupinu prostředků pro uložení virtuálního počítače.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. 
7. V části **Nastavení**podle potřeby proveďte změny a vyberte **OK**. 
8. Na stránce Souhrn byste měli vidět název Image uvedený jako **privátní obrázek**. Výběrem **OK** spusťte nasazení virtuálního počítače.


## <a name="use-powershell"></a>Použití PowerShellu

PowerShell můžete použít k vytvoření virtuálního počítače z Image pomocí zjednodušené sady parametrů pro rutinu [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) . Bitová kopie musí být ve stejné skupině prostředků, ve které vytvoříte virtuální počítač.

 

Zjednodušená sada parametrů pro [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) vyžaduje, abyste zadali název, skupinu prostředků a název Image k vytvoření virtuálního počítače z image. New-AzVm použije hodnotu parametru **-Name** jako název všech prostředků, které vytvoří automaticky. V tomto příkladu poskytujeme podrobnější názvy pro každý z těchto prostředků, ale nechat rutinu je automaticky vytvořit. Můžete také vytvořit prostředky předem, například virtuální síť, a předat název prostředku do rutiny. New-AzVm použije stávající prostředky, pokud je může najít podle jména.

Následující příklad vytvoří virtuální počítač s názvem *myVMFromImage*ve skupině prostředků *myResourceGroup* z image s názvem *myImage*. 


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



## <a name="next-steps"></a>Další kroky
[Vytvoření a správa virtuálních počítačů s Windows pomocí modulu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

