---
title: Vytvořte virtuální počítač s Linuxem v Azure s více síťovými kartami | Microsoft Docs
description: Naučte se vytvořit virtuální počítač s Linuxem s více síťovými kartami připojit se pomocí šablony Azure CLI 2.0 nebo správce prostředků.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 2ad04b17fc4c5bafb7fa0b7eea946832430a4f17
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936747"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Vytvoření virtuální počítač s Linuxem v Azure s více síťových karet
Virtuální počítač (VM) můžete vytvořit v Azure, který má více rozhraní virtuální sítě (NIC) je připojený. Obvyklým scénářem je mít různé podsítě pro připojení front-end a back-end nebo síť vyhrazený pro řešení monitorování nebo zálohování. Tento článek podrobně popisuje postup vytvoření virtuálního počítače s více síťovými kartami k němu připojen a postup přidání nebo odebrání síťové adaptéry ze stávajícího virtuálního počítače. Různé [velikosti virtuálních počítačů](sizes.md) podporu různých počet síťových adaptérů, takže odpovídajícím způsobem upravit velikost virtuálního počítače.

Tento článek podrobně popisuje postup vytvoření virtuálního počítače s více síťovými kartami s Azure CLI 2.0. 


## <a name="create-supporting-resources"></a>Vytvoření doprovodné materiály
Nainstalujte si nejnovější verzi [Azure CLI 2.0](/cli/azure/install-az-cli2) a přihlaste se k Azure účet pomocí [az přihlášení](/cli/azure/reference-index#az_login).

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Názvy parametrů příklad zahrnuté *myResourceGroup*, *můj_účet_úložiště*, a *Můjvp*.

Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Vytvoření virtuální sítě s [vytvoření sítě vnet az](/cli/azure/network/vnet#az_network_vnet_create). Následující příklad vytvoří virtuální síť s názvem *myVnet* a podsíť s názvem *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

Vytvořte podsíť pro provoz back-end s [az sítě vnet podsíť vytváření](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Následující příklad vytvoří podsíť s názvem *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

Vytvořit skupinu zabezpečení sítě s [vytvořit az sítě nsg](/cli/azure/network/nsg#az_network_nsg_create). Následující příklad vytvoří skupinu zabezpečení sítě *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Vytvořit a nakonfigurovat několik síťových adaptérů
Vytvořte dva síťové adaptéry se [vytvořit síťových adaptérů sítě az](/cli/azure/network/nic#az_network_nic_create). Následující příklad vytvoří dva síťové adaptéry s názvem *myNic1* a *myNic2*, připojené skupinu zabezpečení sítě s jednou síťovou KARTOU připojení ke každé podsítě:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Vytvoření virtuálního počítače a připojte síťové karty
Při vytváření virtuálního počítače, zadejte tyto adaptéry jste vytvořili pomocí `--nics`. Musíte také ujistěte se, když vyberete velikost virtuálního počítače. Existují omezení pro celkový počet síťových adaptérů, které můžete přidat k virtuálnímu počítači. Další informace o [velikosti virtuálního počítače s Linuxem](sizes.md). 

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Přidání směrovacích tabulek do hostovaného operačního systému pomocí kroků v [konfigurace hostovaný operační systém pro několik síťových adaptérů](#configure-guest-os-for- multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Přidat síťovou kartu k virtuálnímu počítači
V předchozím postupu vytvořili virtuální počítač s více síťovými kartami. Síťové adaptéry můžete také přidat na existující virtuální počítač s Azure CLI 2.0. Různé [velikosti virtuálních počítačů](sizes.md) podporu různých počet síťových adaptérů, takže odpovídajícím způsobem upravit velikost virtuálního počítače. V případě potřeby můžete [změnit velikost virtuálního počítače](change-vm-size.md).

Vytvořte další síťová karta s [vytvořit síťových adaptérů sítě az](/cli/azure/network/nic#az_network_nic_create). Následující příklad vytvoří síťový adaptér s názvem *myNic3* připojené k back-end podsíť a síť skupiny zabezpečení vytvořené v předchozích krocích:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Chcete-li přidat síťový adaptér na existující virtuální počítač, nejprve zrušit přidělení virtuálního počítače s [az OM deallocate](/cli/azure/vm#az_vm_deallocate). Následující příklad zruší přidělení virtuálního počítače s názvem *Můjvp*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Přidat síťovou kartu s [přidat síťový adaptér virtuálního počítače az](/cli/azure/vm/nic#az_vm_nic_add). Následující příklad přidá *myNic3* k *Můjvp*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Spusťte virtuální počítač s [spuštění virtuálního počítače az](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Přidání směrovacích tabulek do hostovaného operačního systému pomocí kroků v [konfigurace hostovaný operační systém pro několik síťových adaptérů](#configure-guest-os-for- multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Odeberte síťový adaptér z virtuálního počítače
Odebrat síťový adaptér ze stávajícího virtuálního počítače, nejprve zrušit přidělení virtuálního počítače s [az OM deallocate](/cli/azure/vm#az_vm_deallocate). Následující příklad zruší přidělení virtuálního počítače s názvem *Můjvp*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Odeberte na síťový adaptér s [odebrat seskupování virtuálních počítačů az](/cli/azure/vm/nic#az_vm_nic_remove). Následující příklad odebere *myNic3* z *Můjvp*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Spusťte virtuální počítač s [spuštění virtuálního počítače az](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


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

Přidání směrovacích tabulek do hostovaného operačního systému pomocí kroků v [konfigurace hostovaný operační systém pro několik síťových adaptérů](#configure-guest-os-for- multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurace hostovaný operační systém pro několik síťových adaptérů
Pokud přidáte několik síťových adaptérů virtuálního počítače s Linuxem, budete muset vytvořit pravidla směrování. Tato pravidla povolit virtuální počítač odesílat a přijímat provoz, který patří na konkrétní síťový adaptér. Jinak, provoz, který patří do *eth1*, například nemůže zpracovat správně definované výchozí trasu.

Chcete-li tento problém směrování, nejprve přidat do dvou směrovacích tabulek */etc/iproute2/rt_tables* následujícím způsobem:

```bash
echo "200 eth0-rt" >> /etc/iproute2/rt_tables
echo "201 eth1-rt" >> /etc/iproute2/rt_tables
```

Udělat změnu trvalé a použitých při aktivaci zásobník sítě, upravit */etc/sysconfig/network-scripts/ifcfg-eth0* a */etc/sysconfig/network-scripts/ifcfg-eth1*. Příkaz ALTER řádku *"NM_CONTROLLED = yes"* k *"NM_CONTROLLED = ne"*. Bez tohoto kroku další pravidla nebo směrování se nepoužívají automaticky.
 
Dále rozšiřte směrovacích tabulek. Předpokládejme, že máme následující nastavení na místě:

*Směrování*

```bash
default via 10.0.1.1 dev eth0 proto static metric 100
10.0.1.0/24 dev eth0 proto kernel scope link src 10.0.1.4 metric 100
10.0.1.0/24 dev eth1 proto kernel scope link src 10.0.1.5 metric 101
168.63.129.16 via 10.0.1.1 dev eth0 proto dhcp metric 100
169.254.169.254 via 10.0.1.1 dev eth0 proto dhcp metric 100
```

*Rozhraní*

```bash
lo: inet 127.0.0.1/8 scope host lo
eth0: inet 10.0.1.4/24 brd 10.0.1.255 scope global eth0    
eth1: inet 10.0.1.5/24 brd 10.0.1.255 scope global eth1
```

By pak vytvořte následující soubory a přidejte směrování a příslušná pravidla pro každé:

- */etc/sysconfig/network-scripts/rule-eth0*

    ```bash
    from 10.0.1.4/32 table eth0-rt
    to 10.0.1.4/32 table eth0-rt
    ```

- */etc/sysconfig/network-scripts/route-eth0*

    ```bash
    10.0.1.0/24 dev eth0 table eth0-rt
    default via 10.0.1.1 dev eth0 table eth0-rt
    ```

- */etc/sysconfig/network-scripts/rule-eth1*

    ```bash
    from 10.0.1.5/32 table eth1-rt
    to 10.0.1.5/32 table eth1-rt
    ```

- */etc/sysconfig/network-scripts/route-eth1*

    ```bash
    10.0.1.0/24 dev eth1 table eth1-rt
    default via 10.0.1.1 dev eth1 table eth1-rt
    ```

Aby se změny projevily, restartujte *sítě* služby následujícím způsobem:

```bash
systemctl restart network
```

Pravidla směrování jsou nyní správně na místě a můžete připojit buď rozhraní podle potřeby.


## <a name="next-steps"></a>Další postup
Zkontrolujte [velikosti virtuálního počítače s Linuxem](sizes.md) při pokusu o vytvoření virtuálního počítače s více síťovými kartami. Věnujte pozornost maximální počet síťových adaptérů podporuje každý velikost virtuálního počítače. 
