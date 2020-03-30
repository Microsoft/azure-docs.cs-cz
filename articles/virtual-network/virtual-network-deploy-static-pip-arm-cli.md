---
title: Vytvoření virtuálního počítače se statickou veřejnou IP adresou – Azure CLI | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit virtuální počítač se statickou veřejnou IP adresou pomocí rozhraní příkazového řádku Azure (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 4b8f91d7d9fc414ed0ae6387c25f71b1601e0b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76043406"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Vytvoření virtuálního počítače se statickou veřejnou IP adresou pomocí azure cli

Můžete vytvořit virtuální počítač se statickou veřejnou IP adresou. Veřejná IP adresa umožňuje komunikovat s virtuálním počítačem z internetu. Přiřaďte statickou veřejnou IP adresu, nikoli dynamickou adresu, abyste zajistili, že se adresa nikdy nezmění. Další informace o [statických veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#allocation-method). Pokud chcete změnit veřejnou IP adresu přiřazenou existujícímu virtuálnímu počítači z dynamického na statický nebo pracovat se soukromými IP adresami, přečtěte si informace o [přidání, změně nebo odebrání IP adres](virtual-network-network-interface-addresses.md). Veřejné IP adresy mají [nominální poplatek](https://azure.microsoft.com/pricing/details/ip-addresses)a počet veřejných IP adres, které můžete použít na jedno předplatné, je [omezen.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Můžete provést následující kroky z místního počítače nebo pomocí Azure Cloud Shell. Chcete-li používat místní počítač, ujistěte se, že máte [nainstalovaný Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud chcete použít Prostředí Azure Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu libovolného příkazového pole, které následuje. Cloud Shell vás přihlásí do Azure.

1. Pokud používáte cloudové prostředí, přejděte ke kroku 2. Otevřete relaci příkazů a `az login`přihlaste se do Azure pomocí aplikace .
2. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků v oblasti East US Azure:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az-vm-create). Tato `--public-ip-address-allocation=static` možnost přiřadí virtuálnímu počítači statickou veřejnou IP adresu. Následující příklad vytvoří virtuální počítač Ubuntu se statickou, základní veřejnou IP adresou SKU s názvem *myPublicIpAddress*:

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   Pokud veřejná IP adresa musí být `--public-ip-sku Standard` standardní skladová položka, přidejte k předchozímu příkazu. Další informace o [sus veřejných IP adres](virtual-network-ip-addresses-overview-arm.md#sku). Pokud virtuální počítač bude přidán do back-endového fondu veřejného nástroje pro vyrovnávání zatížení Azure, skladová položka veřejné IP adresy virtuálního počítače se musí shodovat s skladovou položkou veřejné IP adresy nástroje pro vyrovnávání zatížení. Podrobnosti najdete [v tématu Azure Load Balancer](../load-balancer/concepts-limitations.md#skus).

4. Zobrazení přiřazené veřejné IP adresy a potvrzení, že byla vytvořena jako statická, základní adresa Skladové položky s [veřejnou ip show az sítě](/cli/azure/network/public-ip#az-network-public-ip-show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure přiřadil veřejnou IP adresu z adres používaných v oblasti, ve které jste virtuální počítač vytvořili. Můžete si stáhnout seznam rozsahů (předpon) pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Neupravujte nastavení IP adresy v operačním systému virtuálního počítače. Operační systém neví o veřejných IP adresách Azure. I když můžete do operačního systému přidat nastavení privátní IP adresy, doporučujeme, abyste tak neučinili, pokud to není nutné, a to až po přečtení [Přidat soukromou IP adresu do operačního systému](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít příkaz [az group delete](/cli/azure/group#az-group-delete):

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

- Další informace o [veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) v Azure
- Další informace o všech [nastaveních veřejných IP adres](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Další informace o [privátních IP adresách](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) a přiřazení [statické privátní IP adresy](virtual-network-network-interface-addresses.md#add-ip-addresses) k virtuálnímu počítači Azure
- Další informace o vytváření virtuálních počítačů [s Linuxem](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
