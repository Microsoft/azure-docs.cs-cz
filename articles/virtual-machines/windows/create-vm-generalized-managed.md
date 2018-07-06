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
ms.date: 03/27/2017
ms.author: cynthn
ms.openlocfilehash: 6baf784068b1fba0c35d2848b8d2dda4f1064a2d
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867976"
---
# <a name="create-a-vm-from-a-managed-image"></a>Vytvoření virtuálního počítače ze spravované image

Vytvoření několika virtuálních počítačů ze spravované image virtuálního počítače pomocí Powershellu nebo na webu Azure portal. Spravované image virtuálního počítače obsahuje informace potřebné k vytvoření virtuálního počítače, včetně operačního systému a datové disky. Virtuální pevné disky, které tvoří image, včetně disků operačního systému a všechny datové disky, jsou uloženy jako spravované disky. 

Musíte mít již [vytvoření spravované image virtuálního počítače](capture-image-resource.md) pro použití při vytváření nového virtuálního počítače. 

## <a name="use-the-portal"></a>Použití portálu

1. Otevřete web [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **Všechny prostředky**. Můžete seřadit prostředky podle **typ** snadno najít Image.
3. Vyberte bitovou kopii, kterou chcete použít v seznamu. Na obrázku **přehled** otevře se stránka.
4. Klikněte na tlačítko **+ vytvořit virtuální počítač** z nabídky.
5. Zadejte informace o virtuálním počítači. Uživatelské jméno a heslo, které tady zadáte, se používá k přihlášení k virtuálnímu počítači. Jakmile budete hotovi, klikněte na **OK**. Můžete vytvořit nový virtuální počítač v existující skupinu prostředků nebo vyberte **vytvořit nový** a vytvořte novou skupinu prostředků pro uložení virtuálního počítače.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. 
7. V části **nastavení**, proveďte potřebné změny a klikněte na tlačítko **OK**. 
8. Na stránce Souhrn, byste měli vidět vaše uveden název image **privátní image**. Klikněte na tlačítko **Ok** spusťte nasazení virtuálního počítače.


## <a name="use-powershell"></a>Použití prostředí PowerShell

Můžete použít PowerShell k vytvoření virtuálního počítače z image pomocí zjednodušené sady parametrů [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) rutiny. Na obrázku musí být ve stejné skupině prostředků, ve kterém chcete vytvořit virtuální počítač.

Tento příklad vyžaduje AzureRM modulu verze 5.6.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Zjednodušené parametrem nastaveným pro New-AzureRmVm vyžaduje pouze, že zadáte jméno, název skupiny a bitové kopie prostředků k vytvoření virtuálního počítače z image, ale použije hodnota **– název** parametr jako název všechny prostředky, že vytvoří automaticky. V tomto příkladu jsme poskytují podrobnější názvy pro každý prostředek, ale nechat tuto rutinu je automaticky vytvořit. Můžete také vytvořit prostředky, jako jsou virtuální sítě, předem domluvili a předat název do rutiny. Pokud jej lze najít podle názvu se bude používat stávající prostředky.

Následující příklad vytvoří virtuální počítač s názvem *myVMFromImage*v *myResourceGroup* skupinu prostředků z image s názvem *myImage*. 


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



## <a name="next-steps"></a>Další postup
Ke správě vašeho nového virtuálního počítače pomocí Azure Powershellu, najdete v článku [vytvoření a správa virtuálních počítačů s Windows pomocí modulu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

