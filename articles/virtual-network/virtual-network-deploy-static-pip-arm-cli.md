---
title: Vytvoření virtuálního počítače se statickou veřejnou IP adresou – Azure CLI | Microsoft Docs
description: Naučte se, jak vytvořit virtuální počítač se statickou veřejnou IP adresou pomocí rozhraní příkazového řádku Azure (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 110f5ab4f0912ae264eb1dc4b649a40cc40f4e12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84703085"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Vytvoření virtuálního počítače se statickou veřejnou IP adresou pomocí Azure CLI

Můžete vytvořit virtuální počítač se statickou veřejnou IP adresou. Veřejná IP adresa vám umožní komunikovat s virtuálním počítačem z Internetu. Přiřaďte statickou veřejnou IP adresu, nikoli dynamickou adresu, abyste zajistili, že se adresa nikdy nemění. Přečtěte si další informace o [statických veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#allocation-method). Postup změny veřejné IP adresy přiřazené existujícímu virtuálnímu počítači z dynamického na statickou nebo pro práci s privátními IP adresami najdete v tématu [Přidání, změna nebo odebrání IP adres](virtual-network-network-interface-addresses.md). Veřejné IP adresy mají [nominální poplatek](https://azure.microsoft.com/pricing/details/ip-addresses)a [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) počtu veřejných IP adres, které můžete použít v rámci předplatného.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Následující kroky můžete provést z místního počítače nebo pomocí Azure Cloud Shell. Pokud chcete použít místní počítač, ujistěte se, že máte nainstalované rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud chcete použít Azure Cloud Shell, vyberte **vyzkoušet** v pravém horním rohu libovolného příkazového řádku, který následuje. Cloud Shell vás přihlásí k Azure.

1. Pokud používáte Cloud Shell, přejděte ke kroku 2. Otevřete relaci příkazu a přihlaste se k Azure pomocí `az login` .
2. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků v Východní USA oblasti Azure:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az-vm-create). `--public-ip-address-allocation=static`Možnost přiřadí virtuálnímu počítači statickou veřejnou IP adresu. Následující příklad vytvoří virtuální počítač s Ubuntu se statickou veřejnou IP adresou (Basic) SKU s názvem *myPublicIpAddress*:

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

   Pokud veřejná IP adresa musí být standardní SKU, přidejte `--public-ip-sku Standard` k předchozímu příkazu. Přečtěte si další informace o [SKU veřejných IP adres](virtual-network-ip-addresses-overview-arm.md#sku). Pokud bude virtuální počítač přidán do fondu back-end veřejné Azure Load Balancer, musí SKU veřejné IP adresy virtuálního počítače odpovídat SKU veřejné IP adresy nástroje pro vyrovnávání zatížení. Podrobnosti najdete v tématu [Azure Load Balancer](../load-balancer/skus.md).

4. Zobrazte přiřazenou veřejnou IP adresu a potvrďte, že byla vytvořená jako statická, základní adresa SKU, pomocí [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure přiřadil veřejnou IP adresu z adres používaných v oblasti, ve které jste virtuální počítač vytvořili. Můžete si stáhnout seznam rozsahů (předpon) pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Neměňte nastavení IP adresy v operačním systému virtuálního počítače. Operační systém neznáte veřejné IP adresy Azure. I když můžete do operačního systému přidat nastavení privátních IP adres, doporučujeme, abyste to neučinili, pokud není potřeba, a ne až po čtení [Přidání privátní IP adresy do operačního systému](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít příkaz [az group delete](/cli/azure/group#az-group-delete):

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

- Další informace o [veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) v Azure
- Další informace o všech [nastaveních veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Další informace o [privátních IP adresách](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) a přiřazení [statických privátních IP adres](virtual-network-network-interface-addresses.md#add-ip-addresses) k virtuálnímu počítači Azure
- Další informace o vytváření virtuálních počítačů se systémy [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
