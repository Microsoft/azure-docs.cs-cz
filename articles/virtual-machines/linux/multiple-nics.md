---
title: Vytvoření virtuálního počítače se systémem Linux v Azure s několika síťovými kartami
description: Přečtěte si, jak vytvořit virtuální počítač se systémem Linux s více připojenými síťovými kartami pomocí šablon Azure CLI nebo Správce prostředků.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: c0eea74890665297a0d450c8afd0a5d60dd1ae00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551806"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Vytvoření virtuálního počítače se systémem Linux v Azure s několika síťovými kartami


Tento článek podrobně popisuje, jak vytvořit virtuální počítač s více síťovými kartami pomocí Azure CLI.

## <a name="create-supporting-resources"></a>Vytvoření podpůrných prostředků
Nainstalujte si nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlaste se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).

V následujících příkladech nahraďte příklady názvů parametrů vlastními hodnotami. Příklady názvů parametrů zahrnují *myResourceGroup*, *mystorageaccount* a *myVM*.

Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální síť pomocí [AZ Network VNet Create](/cli/azure/network/vnet). Následující příklad vytvoří virtuální síť s názvem *myVnet* a podsíť s názvem *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Vytvořte podsíť pro back-end přenos pomocí [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet). Následující příklad vytvoří podsíť s názvem *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Vytvořte skupinu zabezpečení sítě pomocí [AZ Network NSG Create](/cli/azure/network/nsg). Následující příklad vytvoří skupinu zabezpečení sítě *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Vytvoření a konfigurace více síťových karet
Pomocí [AZ Network nic Create vytvořte](/cli/azure/network/nic)dvě síťové karty. Následující příklad vytvoří dvě síťová rozhraní s názvem *myNic1* a *myNic2*, připojenou ke skupině zabezpečení sítě s jednou síťovou kartou připojujícími se ke každé podsíti:

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

## <a name="create-a-vm-and-attach-the-nics"></a>Vytvoření virtuálního počítače a připojení síťových karet
Když vytváříte virtuální počítač, zadejte síťové karty, pomocí kterých jste vytvořili `--nics` . Při výběru velikosti virtuálního počítače se také musíte postarat. Existují omezení pro celkový počet síťových adaptérů, které můžete přidat do virtuálního počítače. Přečtěte si víc o [velikostech virtuálních počítačů se systémem Linux](../sizes.md).

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Následující příklad vytvoří virtuální počítač s názvem *myVM*:

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

Přidejte směrovací tabulky do hostovaného operačního systému, a to provedením kroků v části [Konfigurace hostovaného operačního systému pro více síťových karet](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Přidání síťového rozhraní do virtuálního počítače
Předchozí kroky vytvořily virtuální počítač s více síťovými kartami. Můžete taky přidat síťové karty do existujícího virtuálního počítače pomocí Azure CLI. Různé [velikosti virtuálních počítačů](../sizes.md) podporují proměnlivý počet síťových adaptérů, proto si odpovídajícím způsobem nasaďte velikost svého virtuálního počítače. V případě potřeby můžete [změnit velikost virtuálního počítače](change-vm-size.md).

Vytvořte další síťovou kartu pomocí [AZ Network nic Create](/cli/azure/network/nic). Následující příklad vytvoří síťovou kartu s názvem *myNic3* připojenou k back-endové podsíti a skupině zabezpečení sítě vytvořenou v předchozích krocích:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Pokud chcete přidat síťové rozhraní k existujícímu virtuálnímu počítači, nejdřív nasaďte virtuální počítač pomocí [AZ VM disallocate](/cli/azure/vm). Následující příklad zruší přidělení virtuálního počítače s názvem *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Přidejte síťovou kartu pomocí [AZ VM nic Add](/cli/azure/vm/nic). Následující příklad přidá *myNic3* do *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Spusťte virtuální počítač pomocí [AZ VM Start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Přidejte směrovací tabulky do hostovaného operačního systému, a to provedením kroků v části [Konfigurace hostovaného operačního systému pro více síťových karet](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Odebrání síťového rozhraní z virtuálního počítače
Pokud chcete odebrat síťovou kartu z existujícího virtuálního počítače, nejdřív nasaďte virtuální počítač pomocí [AZ VM disallocate](/cli/azure/vm). Následující příklad zruší přidělení virtuálního počítače s názvem *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Odeberte síťovou kartu pomocí [AZ VM nic Remove](/cli/azure/vm/nic). Následující příklad odebere *myNic3* z *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Spusťte virtuální počítač pomocí [AZ VM Start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Vytvoření více síťových karet pomocí šablon Správce prostředků
Azure Resource Manager šablony používají k definování vašeho prostředí deklarativní soubory JSON. Můžete si přečíst [přehled Azure Resource Manager](../../azure-resource-manager/management/overview.md). Šablony Správce prostředků poskytují způsob vytvoření více instancí prostředku během nasazování, například vytvoření více síťových karet. Pomocí *Kopírovat* určíte počet instancí, které se mají vytvořit:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Přečtěte si další informace o [vytváření více instancí pomocí *kopírování*](../../azure-resource-manager/templates/copy-resources.md). 

Můžete také použít a `copyIndex()` k názvu prostředku připojit číslo, což vám umožní vytvořit `myNic1` , `myNic2` atd. Následující příklad ukazuje, jak připojit hodnotu indexu:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Můžete si přečíst kompletní příklad [vytvoření více síťových karet pomocí šablon Správce prostředků](../../virtual-network/template-samples.md).

Přidejte směrovací tabulky do hostovaného operačního systému, a to provedením kroků v části [Konfigurace hostovaného operačního systému pro více síťových karet](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurace hostovaného operačního systému pro více síťových karet

Předchozí kroky vytvořily virtuální síť a podsíť, připojené síťové karty a pak vytvořily virtuální počítač. Nelze vytvořit pravidla veřejné IP adresy a skupiny zabezpečení sítě, která umožňují provoz protokolu SSH. Pokud chcete nakonfigurovat hostovaný operační systém pro víc síťových adaptérů, musíte na virtuálním počítači zapnout vzdálená připojení a spustit příkazy místně.

Pokud chcete povolený provoz SSH, vytvořte pravidlo skupiny zabezpečení sítě pomocí příkazu [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) následujícím způsobem:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Vytvořte veřejnou IP adresu pomocí [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) a přiřaďte ji k PRVNÍmu síťovému rozhraní pomocí [AZ Network nic IP-config Update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Veřejnou IP adresu virtuálního počítače zobrazíte pomocí příkazu [AZ VM show](/cli/azure/vm#az-vm-show) následujícím způsobem:

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Teď můžete SSH na veřejnou IP adresu vašeho virtuálního počítače. Výchozí uživatelské jméno, které jste zadali v předchozím kroku, bylo *azureuser*. Zadejte své uživatelské jméno a veřejnou IP adresu:

```bash
ssh azureuser@137.117.58.232
```

Pro odesílání do nebo ze sekundárního síťového rozhraní je nutné ručně přidat trvalé trasy k operačnímu systému pro každé sekundární síťové rozhraní. V tomto článku je *eth1* sekundárním rozhraním. Pokyny pro přidání trvalých tras k operačnímu systému se liší podle distribuce. Pokyny najdete v dokumentaci k distribuce.

Při přidávání trasy k operačnímu systému je adresa brány *1* , pro každou podsíť, ve které se síťové rozhraní nachází. Například pokud se síťovému rozhraní přiřadí adresa *10.0.2.4*, brána, kterou zadáte pro trasu, je *10.0.2.1*. Můžete definovat konkrétní síť pro cíl trasy nebo určit cíl *0.0.0.0*, pokud chcete, aby veškerý provoz rozhraní procházel zadanou bránou. Bránu pro každou podsíť spravuje virtuální síť.

Po přidání trasy pro sekundární rozhraní ověřte, že je trasa ve vaší směrovací tabulce s `route -n` . Následující příklad výstupu je pro směrovací tabulku, která má dvě síťová rozhraní přidaná k virtuálnímu počítači v tomto článku:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

Zkontrolujte, jestli se při restartování trasy, kterou jste přidali, překontroluje směrovací tabulku po restartování. Chcete-li otestovat připojení, můžete zadat následující příkaz, například, kde *eth1* je název sekundárního síťového rozhraní:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Další kroky
Při pokusu o vytvoření virtuálního počítače s několika síťovými kartami si Projděte [velikost virtuálních počítačů se systémem Linux](../sizes.md) . Věnujte pozornost maximálnímu počtu síťových adaptérů, které každá velikost virtuálního počítače podporuje.

K lepšímu zabezpečení virtuálních počítačů použijte přístup k VIRTUÁLNÍmu počítači podle potřeby. Tato funkce otevře pravidla skupiny zabezpečení sítě pro provoz SSH v případě potřeby a v určeném časovém období. Další informace najdete v tématu popisujícím [správu přístupu k virtuálním počítačům pomocí metody právě včas](../../security-center/security-center-just-in-time.md).