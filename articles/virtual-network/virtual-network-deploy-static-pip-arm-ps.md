---
title: Vytvoření virtuálního virtuálního počítačů se statickou veřejnou IP adresou – PowerShell | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit virtuální hospodařilo se statickou veřejnou IP adresou pomocí PowerShellu.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 0eb4f86a2484486658171ab4b099794e4ba3e4bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76043393"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Vytvoření virtuálního počítače se statickou veřejnou IP adresou pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete vytvořit virtuální počítač se statickou veřejnou IP adresou. Veřejná IP adresa umožňuje komunikovat s virtuálním počítačem z internetu. Přiřaďte statickou veřejnou IP adresu, nikoli dynamickou adresu, abyste zajistili, že se adresa nikdy nezmění. Další informace o [statických veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#allocation-method). Pokud chcete změnit veřejnou IP adresu přiřazenou existujícímu virtuálnímu počítači z dynamického na statický nebo pracovat se soukromými IP adresami, přečtěte si informace o [přidání, změně nebo odebrání IP adres](virtual-network-network-interface-addresses.md). Veřejné IP adresy mají [nominální poplatek](https://azure.microsoft.com/pricing/details/ip-addresses)a počet veřejných IP adres, které můžete použít na jedno předplatné, je [omezen.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Můžete provést následující kroky z místního počítače nebo pomocí Azure Cloud Shell. Pokud chcete používat místní počítač, ujistěte se, že máte [nainstalovaný Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud chcete použít Prostředí Azure Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu libovolného příkazového pole, které následuje. Cloud Shell vás přihlásí do Azure.

1. Pokud používáte cloudové prostředí, přejděte ke kroku 2. Otevřete relaci příkazů a `Connect-AzAccount`přihlaste se do Azure pomocí aplikace .
2. Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Následující příklad vytvoří skupinu prostředků v oblasti East US Azure:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Vytvořte virtuální počítač pomocí příkazu [New-AzVM.](/powershell/module/az.Compute/New-azVM) Tato `-AllocationMethod "Static"` možnost přiřadí virtuálnímu počítači statickou veřejnou IP adresu. Následující příklad vytvoří virtuální počítač se systémem Windows Server se statickou, základní veřejnou IP adresou skladové položky s názvem *myPublicIpAddress*. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které se má použít jako přihlašovací pověření pro virtuální počítač:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Pokud veřejná adresa IP musí být standardní skladovou [položkou,](virtual-network-public-ip-address.md#create-a-public-ip-address)musíte vytvořit veřejnou IP adresu , vytvořit síťové [rozhraní](virtual-network-network-interface.md#create-a-network-interface), [přiřadit síťovou adresu IP](virtual-network-network-interface-addresses.md#add-ip-addresses)a potom vytvořit virtuální počítač se [síťovým rozhraním](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm)v samostatných krocích. Další informace o [sus veřejných IP adres](virtual-network-ip-addresses-overview-arm.md#sku). Pokud virtuální počítač bude přidán do back-endového fondu veřejného nástroje pro vyrovnávání zatížení Azure, skladová položka veřejné IP adresy virtuálního počítače se musí shodovat s skladovou položkou veřejné IP adresy nástroje pro vyrovnávání zatížení. Podrobnosti najdete [v tématu Azure Load Balancer](../load-balancer/concepts-limitations.md#skus).

4. Zobrazení přiřazené veřejné IP adresy a potvrzení, že byla vytvořena jako statická adresa pomocí [adresy Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure přiřadil veřejnou IP adresu z adres používaných v oblasti, ve které jste virtuální počítač vytvořili. Můžete si stáhnout seznam rozsahů (předpon) pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Neupravujte nastavení IP adresy v operačním systému virtuálního počítače. Operační systém neví o veřejných IP adresách Azure. I když můžete do operačního systému přidat nastavení privátní IP adresy, doporučujeme, abyste tak neučinili, pokud to není nutné, a to až po přečtení [Přidat soukromou IP adresu do operačního systému](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, můžete [odebrat-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) odebrat skupinu prostředků a všechny prostředky, které obsahuje:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

- Další informace o [veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) v Azure
- Další informace o všech [nastaveních veřejných IP adres](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Další informace o [privátních IP adresách](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) a přiřazení [statické privátní IP adresy](virtual-network-network-interface-addresses.md#add-ip-addresses) k virtuálnímu počítači Azure
- Další informace o vytváření virtuálních počítačů [s Linuxem](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
