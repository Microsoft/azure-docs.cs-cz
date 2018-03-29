---
title: Vytvoření virtuálního počítače ze spravovaných bitové kopie v Azure | Microsoft Docs
description: Vytvoření virtuálního počítače Windows z zobecněný spravované bitové kopie pomocí prostředí Azure PowerShell nebo portálu Azure v modelu nasazení Resource Manager.
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
ms.openlocfilehash: 1d543bd9590664e74cff70cf55e8f7bd42f2c6f0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-vm-from-a-managed-image"></a>Vytvoření virtuálního počítače ze spravovaných bitové kopie

Můžete vytvořit víc virtuálních počítačů ze spravovaných image virtuálního počítače pomocí prostředí PowerShell nebo portálu Azure. Spravované image virtuálního počítače obsahuje informace potřebné k vytvoření virtuálního počítače, včetně disků operačního systému a data. Virtuální pevné disky, které tvoří bitovou kopii, včetně disků operačního systému a všech datových disků, jsou uloženy jako spravované disky. 

Musíte mít již [vytvořit spravované image virtuálního počítače](capture-image-resource.md) použít pro vytvoření nového virtuálního počítače. 

## <a name="use-the-portal"></a>Použití portálu

1. Otevřete web [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **Všechny prostředky**. Můžete řadit zdroje podle **typ** snadno najít obrázků.
3. Vyberte bitovou kopii, kterou chcete použít v seznamu. Obrázek **přehled** otevře se stránka.
4. Klikněte na tlačítko **+ vytvořit virtuální počítač** z nabídky.
5. Zadejte informace o virtuálním počítači. Uživatelské jméno a heslo, které tady zadáte, se používá k přihlášení k virtuálnímu počítači. Jakmile budete hotovi, klikněte na **OK**. Můžete vytvořit nový virtuální počítač v existující skupině Resrouce nebo zvolte **vytvořit nový** vytvořit novou skupinu prostředků pro uložení virtuálního počítače.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. 
7. V části **nastavení**, podle potřeby proveďte změny a klikněte na **OK**. 
8. Na stránce Souhrn byste měli vidět název bitové kopie uvedené pro **privátní image**. Klikněte na tlačítko **Ok** ke spuštění nasazení virtuálního počítače.


## <a name="use-powershell"></a>Použití prostředí PowerShell

Můžete použít PowerShell k vytvoření virtuálního počítače z image pomocí zjednodušené samostatné sady parametrů [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) rutiny. Obrázek musí být ve stejné skupině prostředků, kde chcete vytvořit virtuální počítač.

Tento příklad vyžaduje AzureRM verze modulu 5.6.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Parametr zjednodušené nastavení pro New-AzureRmVm vyžaduje pouze, že zadáte název, název skupiny a bitové kopie prostředků k vytvoření virtuálního počítače z image, ale použije hodnotu **-název** parametr jako název všechny prostředky, aby vytvoří automaticky. V tomto příkladu jsme poskytují podrobnější názvy pro každý prostředek, ale nechat automaticky vytvořit rutiny. Můžete také vytvořit prostředky, jako je virtuální sítě, předem a předat do rutiny název. Pokud ho můžete je vyhledat podle názvu jejich ho budou používat existující prostředky.

Následující příklad vytvoří virtuální počítač s názvem *myVMFromImage*v *myResourceGroup* skupinu prostředků, z bitové kopie s názvem *myImage*. 


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
Ke správě nového virtuálního počítače v prostředí Azure PowerShell najdete [vytvořit a spravovat virtuální počítače Windows pomocí modulu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

