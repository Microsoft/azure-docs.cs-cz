---
title: Vytvoření virtuálního počítače ze spravované image v Azure | Dokumentace Microsoftu
description: Vytvoření virtuálního počítače Windows z generalizované image spravovaného pomocí Azure Powershellu nebo na webu Azure portal, v modelu nasazení Resource Manageru.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 9157765afaa610d207a47e19b73f80ae3898fd68
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977554"
---
# <a name="create-a-vm-from-a-managed-image"></a>Vytvoření virtuálního počítače ze spravované image

Můžete vytvořit několik virtuálních počítačů (VM) z virtuálního počítače Azure spravované image pomocí webu Azure portal nebo Powershellu. Spravované image virtuálního počítače obsahuje informace potřebné k vytvoření virtuálního počítače, včetně operačního systému a datové disky. Virtuální pevné disky (VHD), které tvoří image, včetně disků operačního systému a všechny datové disky se ukládají jako spravované disky. 

Před vytvořením nového virtuálního počítače, budete muset [vytvoření spravované image virtuálního počítače](capture-image-resource.md) chcete použít jako zdroj bitové kopie. 


## <a name="use-the-portal"></a>Použití portálu

1. Otevřete web [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **všechny prostředky**. Můžete seřadit prostředky podle **typ** snadno najít Image.
3. Vyberte bitovou kopii, kterou chcete použít v seznamu. Na obrázku **přehled** otevře se stránka.
4. Vyberte **vytvořit virtuální počítač** z nabídky.
5. Zadejte informace o virtuálním počítači. Uživatelské jméno a heslo, které tady zadáte, se použije k přihlášení k virtuálnímu počítači. Jakmile budete hotovi, vyberte **OK**. Můžete vytvořit nový virtuální počítač v existující skupinu prostředků nebo vyberte **vytvořit nový** a vytvořte novou skupinu prostředků pro uložení virtuálního počítače.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. 
7. V části **nastavení**, proveďte potřebné změny a vyberte **OK**. 
8. Na stránce Souhrn, byste měli vidět název vaší image uvedený jako **privátní image**. Vyberte **Ok** spusťte nasazení virtuálního počítače.


## <a name="use-powershell"></a>Použití prostředí PowerShell

Můžete použít PowerShell k vytvoření virtuálního počítače z image pomocí zjednodušené sady parametrů [rutiny New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) rutiny. Na obrázku musí být ve stejné skupině prostředků, kde vytvoříte virtuální počítač.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

Zjednodušené sady parametrů pro [rutiny New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) vyžaduje pouze, že zadáte název, skupinu prostředků a název image vytvořit virtuální počítač z bitové kopie. Nový-AzVm použije hodnotu **– název** parametr jako název všech prostředků, které se vytvoří automaticky. V tomto příkladu budeme poskytovat podrobnější názvy pro všechny prostředky ale nechat rutiny automaticky vytvořit. Můžete také vytvořit prostředky předem, jako je například virtuální síť a předat název prostředku do rutiny. Nový-AzVm bude používat existující prostředky, pokud jej lze najít podle názvu.

Následující příklad vytvoří virtuální počítač s názvem *myVMFromImage*v *myResourceGroup* skupinu prostředků z image s názvem *myImage*. 


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



## <a name="next-steps"></a>Další postup
[Vytvoření a správa virtuálních počítačů s Windows pomocí modulu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

