---
title: Vytvoření virtuálního počítače s Linuxem v Azure s několika síťovými kartami | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač s Linuxem s více síťovými kartami připojenými k němu pomocí šablon Resource Manageru nebo Azure CLI 2.0.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 257b80c30823be41893be8659845d4fcbc922da3
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932268"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Jak vytvořit virtuální počítač s Linuxem v Azure s několika síťových karet
Vytvoření virtuálního počítače (VM) v Azure, která má několik virtuálních síťových rozhraní (NIC) k němu připojená. Běžný scénář, kdy je, aby různé podsítě pro front-endu a back-end připojení nebo síť vyhrazený pro řešení monitorování nebo zálohování. Tento článek podrobně popisuje vytvoření virtuálního počítače s více síťovými kartami připojenými k němu a postup přidání nebo odebrání síťových rozhraní z existujícího virtuálního počítače. Různé [velikosti virtuálních počítačů](sizes.md) podporují různé počet síťových adaptérů, proto odpovídajícím způsobem upravit velikost virtuálního počítače.

Tento článek podrobně popisuje, jak vytvořit virtuální počítač s několika síťovými kartami s Azure CLI 2.0. 


## <a name="create-supporting-resources"></a>Vytvořte Podpůrné prostředky
Nainstalujte nejnovější [příkazového řádku Azure CLI 2.0](/cli/azure/install-az-cli2) a přihlaste se k Azure pomocí účtu [az login](/cli/azure/reference-index#az_login).

V následujících příkladech nahraďte ukázkové názvy parametrů s vlastními hodnotami. Ukázkové názvy parametrů zahrnutých *myResourceGroup*, *mystorageaccount*, a *myVM*.

Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Vytvoření virtuální sítě s [az network vnet vytvořit](/cli/azure/network/vnet#az_network_vnet_create). Následující příklad vytvoří virtuální síť s názvem *myVnet* a podsíť s názvem *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

Vytvořte podsíť pro back-end provozu s využitím [az podsíti virtuální sítě vytvořit](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Následující příklad vytvoří podsíť s názvem *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

Vytvořte skupinu zabezpečení sítě pomocí [az network nsg vytvořit](/cli/azure/network/nsg#az_network_nsg_create). Následující příklad vytvoří skupinu zabezpečení sítě *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Vytvořit a nakonfigurovat více síťových rozhraní
Vytvořit dva síťové adaptéry s [az network nic vytvořit](/cli/azure/network/nic#az_network_nic_create). Následující příklad vytvoří dva síťové adaptéry s názvem *myNic1* a *myNic2*, připojené skupiny zabezpečení sítě s jednou síťovou KARTOU připojení ke každé podsíti:

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
Při vytváření virtuálního počítače, zadejte síťová rozhraní jste vytvořili pomocí `--nics`. Také je potřeba dbát při volbě velikosti virtuálního počítače. Existují omezení pro celkový počet síťových adaptérů, které můžete přidat do virtuálního počítače. Další informace o [velikosti virtuálního počítače s Linuxem](sizes.md). 

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

Přidání tabulek směrování do hostovaného operačního systému podle postupu uvedeného v [konfigurace hostovaného operačního systému pro několik síťových karet](#configure-guest-os-for- multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Přidat síťové rozhraní k virtuálnímu počítači
V předchozích krocích vytvořili virtuální počítač s několika síťovými kartami. Síťové adaptéry můžete také přidat do existujícího virtuálního počítače pomocí Azure CLI 2.0. Různé [velikosti virtuálních počítačů](sizes.md) podporují různé počet síťových adaptérů, proto odpovídajícím způsobem upravit velikost virtuálního počítače. V případě potřeby můžete [změnit velikost virtuálního počítače](change-vm-size.md).

Vytvořte další síťová karta s [az network nic vytvořit](/cli/azure/network/nic#az_network_nic_create). Následující příklad vytvoří síťové rozhraní s názvem *myNic3* připojené k back endové podsítě a skupinu zabezpečení sítě vytvořené v předchozích krocích:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Chcete-li přidat síťové rozhraní existujícímu virtuálnímu počítači, nejprve zrušte přidělení virtuálního počítače s [az vm deallocate](/cli/azure/vm#az_vm_deallocate). V následujícím příkladu se uvolní virtuální počítač s názvem *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Přidání síťového rozhraní s [az vm nic přidat](/cli/azure/vm/nic#az_vm_nic_add). Následující příklad přidá *myNic3* k *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Spusťte virtuální počítač s [az vm start](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Přidání tabulek směrování do hostovaného operačního systému podle postupu uvedeného v [konfigurace hostovaného operačního systému pro několik síťových karet](#configure-guest-os-for- multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Z virtuálního počítače odeberte síťovou kartu
Odebrání síťové rozhraní z existujícího virtuálního počítače, nejprve zrušte přidělení virtuálního počítače s [az vm deallocate](/cli/azure/vm#az_vm_deallocate). V následujícím příkladu se uvolní virtuální počítač s názvem *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Odebrání síťového adaptéru s [az vm nic odebrat](/cli/azure/vm/nic#az_vm_nic_remove). Následující příklad odebere *myNic3* z *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Spusťte virtuální počítač s [az vm start](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Vytvoření více síťových adaptérů pomocí šablon Resource Manageru
Šablony Azure Resource Manageru pomocí deklarativní soubory JSON definovat vaše prostředí. Můžete si přečíst [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md). Šablony Resource Manageru poskytují způsob, jak vytvořit více instancí prostředku během nasazení, jako je vytvoření více síťových rozhraní. Použijete *kopírování* k určení počtu instancí na vytvoření:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Další informace o [vytvoření více instancí pomocí *kopírování*](../../resource-group-create-multiple.md). 

Můžete také použít `copyIndex()` pak připojte číslo s názvem prostředku, který vám umožní vytvořit `myNic1`, `myNic2`atd. Následuje příklad připojení hodnotu indexu:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Úplný příklad si můžete přečíst [vytvoření více síťových adaptérů pomocí šablon Resource Manageru](../../virtual-network/template-samples.md).

Přidání tabulek směrování do hostovaného operačního systému podle postupu uvedeného v [konfigurace hostovaného operačního systému pro několik síťových karet](#configure-guest-os-for- multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurace hostovaného operačního systému pro více síťových rozhraní
Pokud přidáte více síťových rozhraní virtuálního počítače s Linuxem, je potřeba vytvořit pravidla směrování. Tato pravidla umožňují virtuálnímu počítači odesílat a přijímat provoz, který patří do konkrétní síťový adaptér. Jinak, provoz, který patří do *eth1*, například nemůže být zpracovány správně definovaný výchozí trasu.

Chcete-li tento problém směrování, nejprve přidat dva směrovacích tabulek na */etc/iproute2/rt_tables* následujícím způsobem:

```bash
echo "200 eth0-rt" >> /etc/iproute2/rt_tables
echo "201 eth1-rt" >> /etc/iproute2/rt_tables
```

Chcete-li provést změnu trvalé a použité při aktivaci zásobníku sítě, upravte */etc/sysconfig/network-scripts/ifcfg-eth0* a */etc/sysconfig/network-scripts/ifcfg-eth1*. Příkaz ALTER řádku *"NM_CONTROLLED = Ano"* k *"NM_CONTROLLED = ne"*. Bez tohoto kroku další pravidla/směrování se nepoužijí automaticky.
 
Dále rozšiřte směrovací tabulky. Předpokládejme, že máme následující nastavení na místě:

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

By potom vytvoří následující soubory a přidání příslušných pravidel a tras do každé:

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

Pravidla směrování se nyní správně na místě a podle potřeby se můžete spojit s buď rozhraní.


## <a name="next-steps"></a>Další postup
Kontrola [velikosti virtuálního počítače s Linuxem](sizes.md) při pokusu o vytvoření virtuálního počítače s několika síťovými kartami. Věnujte pozornost maximální počet síťových adaptérů podporuje všechny velikosti virtuálních počítačů. 
