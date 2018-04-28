---
title: Vytvořte virtuální počítač s Linuxem v Azure s více síťovými kartami | Microsoft Docs
description: Naučte se vytvořit virtuální počítač s Linuxem s více síťovými kartami připojit se pomocí šablony Azure CLI nebo správce prostředků.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 5a4e19f232abce8701e6d2e12a876c55079b91a2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-linux-virtual-machine-with-multiple-nics-using-the-azure-cli-10"></a>Vytvořit virtuální počítač s Linuxem s více síťovými kartami pomocí Azure CLI 1.0
Virtuální počítač (VM) můžete vytvořit v Azure, který má více rozhraní virtuální sítě (NIC) je připojený. Obvyklým scénářem je mít různé podsítě pro připojení front-end a back-end nebo síť vyhrazený pro řešení monitorování nebo zálohování. Tento článek obsahuje rychlý příkazů pro vytvoření virtuálního počítače s více síťovými kartami k němu připojen. Různé [velikosti virtuálních počítačů](sizes.md) podporu různých počet síťových adaptérů, takže odpovídajícím způsobem upravit velikost virtuálního počítače.

> [!WARNING]
> Při vytvoření virtuálního počítače – síťovými kartami nelze přidat do existující virtuální počítač s 1.0 rozhraní příkazového řádku Azure, je nutné připojit víc síťových karet. Můžete [přidání síťových adaptérů do stávajícího virtuálního počítače pomocí Azure CLI 2.0](multiple-nics.md). Můžete také [vytvoření virtuálního počítače založené na původní virtuální disky](copy-vm.md) a vytvořte několik síťových adaptérů, jak nasadit virtuální počítač.


## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- [Azure CLI 1.0](#create-supporting-resources) – naše rozhraní příkazového řádku pro classic a resource správu modelech nasazení (v tomto článku)
- [Azure CLI 2.0](multiple-nics.md) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků


## <a name="create-supporting-resources"></a>Vytvoření doprovodné materiály
Ujistěte se, že máte [rozhraní příkazového řádku Azure](../../cli-install-nodejs.md) přihlášení a použití režimu Resource Manager:

```azurecli
azure config mode arm
```

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Názvy parametrů příklad zahrnuté *myResourceGroup*, *můj_účet_úložiště*, a *Můjvp*.

Nejprve vytvořte skupinu prostředků. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
azure group create myResourceGroup --location eastus
```

Vytvořte účet úložiště pro uložení virtuálních počítačů. Následující příklad vytvoří účet úložiště s názvem *můj_účet_úložiště*:

```azurecli
azure storage account create mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --kind Storage \
    --sku-name PLRS
```

Vytvoření virtuální sítě pro virtuální počítače k připojení. Následující příklad vytvoří virtuální síť s názvem *myVnet* s předponu adresy z *192.168.0.0/16*:

```azurecli
azure network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefixes 192.168.0.0/16
```

Vytvořte dvě virtuální sítě podsítě – jednu pro provoz front-endu a jednu pro provoz back-end. Následující příklad vytvoří dvě podsítě, s názvem *mySubnetFrontEnd* a *mySubnetBackEnd*:

```azurecli
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetFrontEnd \
    --address-prefix 192.168.1.0/24
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>Vytvořit a nakonfigurovat několik síťových adaptérů
Si můžete přečíst další informace o [nasazení pomocí rozhraní příkazového řádku Azure několik síťových adaptérů](../../virtual-machines/linux/multiple-nics.md), včetně skriptování proces vytvoření všechny síťové adaptéry ve smyčce.

Následující příklad vytvoří dva síťové adaptéry s názvem *myNic1* a *myNic2*, s jednou síťovou KARTOU připojení ke každé podsítě:

```azurecli
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic1 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetFrontEnd
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic2 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetBackEnd
```

Obvykle můžete také vytvořit [skupinu zabezpečení sítě](../../virtual-network/virtual-networks-nsg.md) nebo [nástroj pro vyrovnávání zatížení](../../load-balancer/load-balancer-overview.md) ke správě a distribuci přenosů mezi virtuální počítače. Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Vytvoření vazby vaše síťové adaptéry na skupinu zabezpečení sítě pomocí `azure network nic set`. Následující příklad vytvoří vazbu *myNic1* a *myNic2* s *myNetworkSecurityGroup*:

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Vytvoření virtuálního počítače a připojte síťové karty
Při vytváření virtuálního počítače, můžete teď určit několik síťových adaptérů. Místo použití `--nic-name` poskytovat jednu síťovou kartu, místo toho je použití `--nic-names` a zadejte čárkami oddělený seznam síťových adaptérů. Musíte také ujistěte se, když vyberete velikost virtuálního počítače. Existují omezení pro celkový počet síťových adaptérů, které můžete přidat k virtuálnímu počítači. Další informace o [velikosti virtuálního počítače s Linuxem](sizes.md). Následující příklad ukazuje, jak určit víc síťových karet a pak velikost virtuálního počítače, který podporuje použití více síťových adaptérů (*Standard_DS2_v2*):

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username azureuser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

Pokud přidáte několik síťových adaptérů virtuálního počítače s Linuxem, budete muset vytvořit pravidla směrování. Tato pravidla povolit virtuální počítač odesílat a přijímat provoz, který patří na konkrétní síťový adaptér. Jinak provoz, který patří do eth1, například nelze zpracovat správně podle definovaného výchozí trasu. Chcete-li tento problém směrování, najdete v části [konfigurovat hostovaný operační systém pro několik síťových adaptérů](multiple-nics.md#configure-guest-os-for-multiple-nics).

## <a name="create-multiple-nics-using-resource-manager-templates"></a>Vytvořit několik síťových adaptérů pomocí šablony Resource Manageru
Šablony Azure Resource Manageru použijte k definování prostředí deklarativní soubory JSON. Můžete si přečíst [přehled nástroje Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Správce prostředků šablony poskytují způsob, jak vytvořit více instancí prostředku během nasazení, jako je například vytváření několik síťových adaptérů. Používáte *kopie* k určení počtu instancí vytvořit:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Další informace o [vytváření více instancí pomocí *kopie*](../../resource-group-create-multiple.md). 

Můžete použít také `copyIndex()` pak připojit k názvu zdroje, které vám umožní vytvořit číslo `myNic1`, `myNic2`atd. Na obrázku je příkladem připojením index hodnoty:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Kompletní příklad, jak si můžete přečíst [vytváření několik síťových adaptérů pomocí šablony Resource Manageru](../../virtual-network/template-samples.md).

Pokud přidáte několik síťových adaptérů virtuálního počítače s Linuxem, budete muset vytvořit pravidla směrování. Tato pravidla povolit virtuální počítač odesílat a přijímat provoz, který patří na konkrétní síťový adaptér. Jinak provoz, který patří do eth1, například nelze zpracovat správně podle definovaného výchozí trasu. Chcete-li tento problém směrování, najdete v části [konfigurovat hostovaný operační systém pro několik síťových adaptérů](multiple-nics.md#configure-guest-os-for-multiple-nics).

## <a name="next-steps"></a>Další postup
Projděte si [velikosti virtuálního počítače s Linuxem](sizes.md) při pokusu o vytvoření virtuálního počítače s více síťovými kartami. Věnujte pozornost maximální počet síťových adaptérů podporuje každý velikost virtuálního počítače. 

Mějte na paměti, že další síťové adaptéry nelze přidat do stávajícího virtuálního počítače, když nasazujete virtuální počítač je nutné vytvořit všechny síťové adaptéry. Vezměte v potaz při plánování nasazení a ujistěte se, že máte všechny požadované síťové připojení od samého počátku.

