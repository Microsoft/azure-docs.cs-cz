---
title: Vytvoření virtuálního počítače ze spravované image v Azure
description: Vytvořte virtuální počítač s Windows z generalizované spravované image pomocí Azure PowerShellu nebo portálu.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: b0c6689b66037067a4c5174738945b7c6fabd5b5
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086313"
---
# <a name="create-a-vm-from-a-managed-image"></a>Vytvoření virtuálního počítače ze spravované image

Pomocí portálu Azure nebo PowerShellu můžete vytvořit víc virtuálních počítačů (Virtuální počítače) z image spravovaného virtuálního počítače Azure. Spravovaná image virtuálního počítače obsahuje informace potřebné k vytvoření virtuálního počítače, včetně operačního systému a datových disků. Virtuální pevné disky (VD), které tvoří bitovou kopii, včetně disků operačního systému a všech datových disků, jsou uloženy jako spravované disky. 

Před vytvořením nového virtuálního počítače budete muset [vytvořit spravovanou image virtuálního počítače,](capture-image-resource.md) abyste ji mohli použít jako zdrojovou bitovou kopii, a udělit přístup ke čtení v bitové kopii všem uživatelům, kteří by měli mít přístup k bitové kopii. 


## <a name="use-the-portal"></a>Použití portálu

1. Přejděte na [portál Azure](https://portal.azure.com) a najděte spravovanou bitovou kopii. Vyhledejte a vyberte **obrázky**.
3. Ze seznamu vyberte obrázek, který chcete použít. Otevře se stránka **Přehled** obrázku.
4. V nabídce vyberte **Vytvořit virtuální hod.**
5. Zadejte informace o virtuálním počítači. Zde zadané uživatelské jméno a heslo bude použito k přihlášení k virtuálnímu počítači. Po dokončení vyberte **OK**. Můžete vytvořit nový virtuální virtuální herd v existující skupině prostředků nebo zvolte **Vytvořit nový** pro vytvoření nové skupiny prostředků pro uložení virtuálního aplikace.
6. Vyberte velikost virtuálního počítače. Chcete-li zobrazit více velikostí, vyberte **možnost Zobrazit vše** nebo změňte filtr **Podporovaného typu disku.** 
7. V části **Nastavení**proveďte podle potřeby změny a vyberte **OK**. 
8. Na souhrnné stránce by se mělo zobrazit název obrázku uvedený jako **soukromý obrázek**. Chcete-li spustit nasazení virtuálního počítače, vyberte **ok.**


## <a name="use-powershell"></a>Použití prostředí PowerShell

Pomocí prostředí PowerShell můžete vytvořit virtuální ho disekonu z obrazu pomocí zjednodušené sady parametrů pro rutinu [New-AzVm.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) Obrázek musí být ve stejné skupině prostředků, kde vytvoříte virtuální ho.

 

Zjednodušená sada parametrů pro [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) vyžaduje pouze zadat název, skupinu prostředků a název bitové kopie k vytvoření virtuálního virtuálního serveru z bitové kopie. New-AzVm použije hodnotu parametru **-Name** jako název všech prostředků, které automaticky vytvoří. V tomto příkladu poskytujeme podrobnější názvy pro každý z prostředků, ale nechat rutinu vytvořit automaticky. Můžete také vytvořit prostředky předem, jako je například virtuální síť, a předat název prostředku do rutiny. New-AzVm bude používat stávající prostředky, pokud je může najít podle jejich názvu.

Následující příklad vytvoří virtuální hod s názvem *myVMFromImage*ve skupině prostředků *myResourceGroup* z obrázku s názvem *myImage*. 


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
[Vytváření a správa virtuálních aplikací Windows pomocí modulu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

