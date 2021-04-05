---
title: Vytvoření virtuálního počítače se statickou veřejnou IP adresou – PowerShell | Microsoft Docs
description: Vytvořte virtuální počítač se statickou veřejnou IP adresou pomocí PowerShellu. Statické veřejné IP adresy jsou adresy, které se nikdy nezmění.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 047a6db82e65c97deba5270d181f72315a67e82c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791252"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Vytvoření virtuálního počítače se statickou veřejnou IP adresou pomocí PowerShellu


Můžete vytvořit virtuální počítač se statickou veřejnou IP adresou. Veřejná IP adresa vám umožní komunikovat s virtuálním počítačem z Internetu. Přiřaďte statickou veřejnou IP adresu, nikoli dynamickou adresu, abyste zajistili, že se adresa nikdy nemění. Přečtěte si další informace o [statických veřejných IP adresách](./public-ip-addresses.md#allocation-method). Postup změny veřejné IP adresy přiřazené existujícímu virtuálnímu počítači z dynamického na statickou nebo pro práci s privátními IP adresami najdete v tématu [Přidání, změna nebo odebrání IP adres](virtual-network-network-interface-addresses.md). Veřejné IP adresy mají [nominální poplatek](https://azure.microsoft.com/pricing/details/ip-addresses)a [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) počtu veřejných IP adres, které můžete použít v rámci předplatného.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Následující kroky můžete provést z místního počítače nebo pomocí Azure Cloud Shell. Pokud chcete použít místní počítač, ujistěte se, že máte [nainstalovanou Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud chcete použít Azure Cloud Shell, vyberte **vyzkoušet** v pravém horním rohu libovolného příkazového řádku, který následuje. Cloud Shell vás přihlásí k Azure.

1. Pokud používáte Cloud Shell, přejděte ke kroku 2. Otevřete relaci příkazu a přihlaste se k Azure pomocí `Connect-AzAccount` .
2. Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Následující příklad vytvoří skupinu prostředků v Východní USA oblasti Azure:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Vytvořte virtuální počítač pomocí příkazu [New-AzVM](/powershell/module/az.Compute/New-azVM) . `-AllocationMethod "Static"`Možnost přiřadí virtuálnímu počítači statickou veřejnou IP adresu. Následující příklad vytvoří virtuální počítač s Windows serverem se statickou veřejnou IP adresou SKU s názvem *myPublicIpAddress*. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které se použije jako přihlašovací údaje pro virtuální počítač:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Pokud veřejná IP adresa musí být standardní SKU, musíte [vytvořit veřejnou IP adresu](virtual-network-public-ip-address.md#create-a-public-ip-address), [vytvořit síťové rozhraní](virtual-network-network-interface.md#create-a-network-interface), [přiřadit veřejnou IP adresu síťovému rozhraní](virtual-network-network-interface-addresses.md#add-ip-addresses)a pak v samostatných krocích [vytvořit virtuální počítač se síťovým rozhraním](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm). Přečtěte si další informace o [SKU veřejných IP adres](./public-ip-addresses.md#sku). Pokud bude virtuální počítač přidán do fondu back-end veřejné Azure Load Balancer, musí SKU veřejné IP adresy virtuálního počítače odpovídat SKU veřejné IP adresy nástroje pro vyrovnávání zatížení. Podrobnosti najdete v tématu [Azure Load Balancer](../load-balancer/skus.md).

4. Zobrazte přiřazenou veřejnou IP adresu a potvrďte, že byla vytvořená jako statická adresa s [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure přiřadil veřejnou IP adresu z adres používaných v oblasti, ve které jste virtuální počítač vytvořili. Můžete si stáhnout seznam rozsahů (předpon) pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Neměňte nastavení IP adresy v operačním systému virtuálního počítače. Operační systém neznáte veřejné IP adresy Azure. I když můžete do operačního systému přidat nastavení privátních IP adres, doporučujeme, abyste to neučinili, pokud není potřeba, a ne až po čtení [Přidání privátní IP adresy do operačního systému](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít [příkaz Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

- Další informace o [veřejných IP adresách](./public-ip-addresses.md#public-ip-addresses) v Azure
- Další informace o všech [nastaveních veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Další informace o [privátních IP adresách](./private-ip-addresses.md) a přiřazení [statických privátních IP adres](virtual-network-network-interface-addresses.md#add-ip-addresses) k virtuálnímu počítači Azure
- Další informace o vytváření virtuálních počítačů se systémy [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
