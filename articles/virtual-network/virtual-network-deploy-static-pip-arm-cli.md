---
title: Vytvoření virtuálního počítače se statickou veřejnou IP adresu – rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač se statickou veřejnou IP adresu pomocí rozhraní příkazového řádku Azure (CLI).
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: 203c886828fa13433f784d1db9a032b06fab398a
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715195"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Vytvoření virtuálního počítače se statickou veřejnou IP adresu pomocí Azure CLI

Vytvoření virtuálního počítače se statickou veřejnou IP adresu. Veřejná IP adresa umožňuje komunikaci k virtuálnímu počítači z Internetu. Přiřadíte statickou veřejnou IP adresu, nikoli s dynamickou adresou, k zajištění, že adresa nikdy nemění. Další informace o [statické veřejné IP adresy](virtual-network-ip-addresses-overview-arm.md#allocation-method). Chcete-li změnit přiřazené do stávajícího virtuálního počítače z dynamické na statickou veřejnou IP adresu pro práci s privátními IP adresami, najdete v článku [přidání, změna nebo odebrání IP adres](virtual-network-network-interface-addresses.md). Mají veřejné IP adresy [nominální poplatek](https://azure.microsoft.com/pricing/details/ip-addresses)a je [limit](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) počtu veřejné IP adresy, které můžete použít jedno předplatné.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Ze svého místního počítače nebo s použitím Azure Cloud Shell, můžete provést následující kroky. Použití místního počítače, ujistěte se, máte [nainstalované Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud chcete použít Azure Cloud Shell, vyberte **vyzkoušet** v pravém horním rohu pole libovolný příkaz, který následuje. Cloud Shell se přihlásíte do Azure.

1. Pokud používáte Cloud Shell, přejděte ke kroku 2. Otevřete relaci příkazového řádku a přihlášení do Azure s využitím `az login`.
2. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků v oblasti Azure USA – východ:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az-vm-create). `--public-ip-address-allocation=static` Možnost statickou veřejnou IP adresu přiřadí k virtuálnímu počítači. Následující příklad vytvoří virtuální počítač Ubuntu s statické, základní SKU veřejné IP adresy s názvem *myPublicIpAddress*:

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

   Pokud je veřejná IP adresa musí být standardní SKU, přidejte `--public-ip-sku Standard` do předchozího příkazu. Další informace o [veřejné IP adresy skladové položky](virtual-network-ip-addresses-overview-arm.md#sku). Pokud virtuální počítač se přidají do fondu back-end veřejný Azure Load Balancer, skladovou Položku virtuálního počítače veřejné IP adresy musí odpovídat SKU nástroje pro vyrovnávání zatížení veřejnou IP adresu. Podrobnosti najdete v tématu [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

4. Zobrazit přiřazenou veřejnou IP adresu a potvrďte, že byl vytvořen jako statickou, základní adresu SKU, [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure přiřadit veřejnou IP adresu z adresy používané v oblasti, kterou jste vytvořili virtuální počítač. Můžete si stáhnout seznam rozsahů (předpon) pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
Neprovádějte žádné změny nastavení IP adresy v rámci operačního systému virtuálního počítače. Operační systém je vědět o Azure veřejné IP adresy. I když nastavení privátní IP adresy můžete přidat do operačního systému, doporučujeme, pokud to neuděláte není-li nutné a nikoli až po čtení [přidejte privátní IP adresu pro operační systém](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít příkaz [az group delete](/cli/azure/group#az-group-delete):

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

- Další informace o [veřejné IP adresy](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) v Azure
- Další informace o všech [nastavení veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Další informace o [privátních IP adres](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) a přiřazení [statickou privátní IP adresou](virtual-network-network-interface-addresses.md#add-ip-addresses) na virtuálním počítači Azure
- Další informace o vytváření [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuálních počítačů