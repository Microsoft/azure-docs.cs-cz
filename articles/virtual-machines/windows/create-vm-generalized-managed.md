---
title: Vytvoření virtuálního počítače ze spravované image v Azure
description: Vytvořte virtuální počítač s Windows ze generalizované spravované Image pomocí Azure PowerShell nebo portálu.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: bc87b0ec9cf41b2ddc5746a9734463f544b0cc2a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550684"
---
# <a name="create-a-vm-from-a-managed-image"></a>Vytvoření virtuálního počítače ze spravované image

Můžete vytvořit několik virtuálních počítačů z image spravovaného virtuálního počítače Azure pomocí Azure Portal nebo PowerShellu. Spravovaná image virtuálního počítače obsahuje informace potřebné k vytvoření virtuálního počítače, včetně operačního systému a datových disků. Virtuální pevné disky (VHD), které tvoří bitovou kopii, včetně disků operačního systému i všech datových disků, jsou uloženy jako spravované disky. 

Před vytvořením nového virtuálního počítače budete muset [vytvořit spravovanou image virtuálního počítače](capture-image-resource.md) , která se použije jako zdrojová image, a udělit všem uživatelům, kteří mají mít přístup k imagi, přístup pro čtení k imagi. 

Jedna spravovaná bitová kopie podporuje až 20 současných nasazení. Při pokusu o vytvoření více než 20 virtuálních počítačů současně ze stejné spravované image může docházet k vypršení časového limitu zřizování z důvodu omezení výkonu úložiště u jednoho virtuálního pevného disku. Pokud chcete vytvořit více než 20 virtuálních počítačů současně, použijte image [Galerie sdílených imagí](../shared-image-galleries.md) nakonfigurovanou s 1 replikou pro každé 20 souběžných nasazení virtuálních počítačů.

## <a name="use-the-portal"></a>Použití portálu

1. Pro vyhledání spravované image použijte [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **obrázky**.
3. Ze seznamu vyberte obrázek, který chcete použít. Otevře se stránka s **přehledem** obrázku.
4. V nabídce vyberte **vytvořit virtuální počítač** .
5. Zadejte informace o virtuálním počítači. Uživatelské jméno a heslo, které tady zadáte, se použije k přihlášení k virtuálnímu počítači. Po dokončení vyberte **OK**. Nový virtuální počítač můžete vytvořit v existující skupině prostředků, nebo výběrem možnosti **vytvořit novou** vytvořte novou skupinu prostředků pro uložení virtuálního počítače.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit vše** nebo změnit filtr **podporovaného typu disku** . 
7. V části **Nastavení** podle potřeby proveďte změny a vyberte **OK**. 
8. Na stránce Souhrn byste měli vidět název Image uvedený jako **privátní obrázek**. Výběrem **OK** spusťte nasazení virtuálního počítače.


## <a name="use-powershell"></a>Použití prostředí PowerShell

PowerShell můžete použít k vytvoření virtuálního počítače z Image pomocí zjednodušené sady parametrů pro rutinu [New-AzVm](/powershell/module/az.compute/new-azvm) . Bitová kopie musí být ve stejné skupině prostředků, ve které vytvoříte virtuální počítač.

 

Zjednodušená sada parametrů pro [New-AzVm](/powershell/module/az.compute/new-azvm) vyžaduje, abyste zadali název, skupinu prostředků a název Image k vytvoření virtuálního počítače z image. New-AzVm použije hodnotu parametru **-Name** jako název všech prostředků, které automaticky vytvoří. V tomto příkladu poskytujeme podrobnější názvy pro každý z těchto prostředků, ale nechat rutinu je automaticky vytvořit. Můžete také vytvořit prostředky předem, například virtuální síť, a předat název prostředku do rutiny. New-AzVm použijí existující prostředky, pokud je nalezne podle jména.

Následující příklad vytvoří virtuální počítač s názvem *myVMFromImage* ve skupině prostředků *myResourceGroup* z image s názvem *myImage*. 


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
[Vytvoření a správa virtuálních počítačů s Windows pomocí modulu Azure PowerShell](tutorial-manage-vm.md)