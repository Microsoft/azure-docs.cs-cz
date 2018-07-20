---
title: Vytvoření virtuálního počítače s Linuxem v Azure s několika síťovými kartami | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač s Linuxem s více síťovými kartami připojenými k němu pomocí šablon Resource Manageru nebo Azure CLI 2.0.
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
ms.date: 06/07/2018
ms.author: iainfou
ms.openlocfilehash: aae71dafd3685e44975049c4287c083abc2330bc
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144852"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Jak vytvořit virtuální počítač s Linuxem v Azure s několika síťových karet
Vytvoření virtuálního počítače (VM) v Azure, která má několik virtuálních síťových rozhraní (NIC) k němu připojená. Běžný scénář, kdy je, aby různé podsítě pro front-endu a back-end připojení nebo síť vyhrazený pro řešení monitorování nebo zálohování. Tento článek podrobně popisuje vytvoření virtuálního počítače s více síťovými kartami připojenými k němu a postup přidání nebo odebrání síťových rozhraní z existujícího virtuálního počítače. Různé [velikosti virtuálních počítačů](sizes.md) podporují různé počet síťových adaptérů, proto odpovídajícím způsobem upravit velikost virtuálního počítače.

Tento článek podrobně popisuje, jak vytvořit virtuální počítač s několika síťovými kartami s Azure CLI 2.0. K provedení těchto kroků můžete také využít [Azure CLI 1.0](multiple-nics-nodejs.md).


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
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Vytvořte podsíť pro back-end provozu s využitím [az podsíti virtuální sítě vytvořit](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Následující příklad vytvoří podsíť s názvem *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
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

V předchozích krocích vytvořili virtuální síť a podsíť, připojené síťové karty a vytvořili virtuální počítač. Nebyly vytvořeny veřejné IP adresy a síťového pravidla skupiny zabezpečení, které umožní provoz SSH. Konfigurace hostovaného operačního systému pro více síťových adaptérů, budete muset povolit vzdálené připojení a spouštění místních příkazů na virtuálním počítači.

Povolit provoz SSH, vytvořte pravidlo skupiny zabezpečení sítě s [az network nsg pravidlo vytvořte](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) následujícím způsobem:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Vytvoření veřejné IP adresy s [az network public-ip vytvořit](/cli/azure/network/public-ip#az-network-public-ip-create) a přiřaďte ho ke první síťové rozhraní s [az network nic ip-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip-address create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip-addres myPublicIP
```

Chcete-li zobrazit zobrazení veřejné IP adresu virtuálního počítače, použijte [az vm show](/cli/azure/vm#az-vm-show) následujícím způsobem:

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Nyní SSH na veřejnou IP adresu vašeho virtuálního počítače. Výchozí uživatelské jméno uvedené v předchozím kroku se *azureuser*. Zadejte své uživatelské jméno a veřejnou IP adresu:

```bash
ssh azureuser@137.117.58.232
```

K odeslání do nebo ze sekundární síťové rozhraní, budete muset ručně přidat trvalé trasy pro operační systém pro každý sekundární síťové rozhraní. V tomto článku *eth1* je sekundární rozhraní. Pokyny pro přidání trvalé trasy v operačním systému se liší podle distribuce. Najdete v dokumentaci vaší distribuce pokyny.

Při přidání trasy pro operační systém, je adresa brány *.1* podle toho, která podsítě síťové rozhraní nachází. Například, pokud je síťové rozhraní je přiřazena adresa *10.0.2.4*, brána zadáte pro trasy je *10.0.2.1*. Můžete definovat konkrétní síť pro cíl vaší trasy, nebo zadat cílového umístění *0.0.0.0*, pokud chcete, aby veškerý provoz pro rozhraní a absolvovat zadané brány. Pro každou podsíť brány se spravuje přes virtuální síť.

Po přidání trasy pro sekundární rozhraní, ověřte, zda trasy ve směrovací tabulce s `route -n`. Následující příklad výstupu je pro směrovací tabulka, která má dvě síťová rozhraní, přidat do virtuálního počítače v tomto článku:

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

Potvrďte, že trasy, které jste přidali se uchovávají napříč restartování počítače tak, že zkontrolujete směrovací tabulku znovu po restartování. Chcete-li otestovat připojení, můžete zadat následující příkaz, například, kde *eth1* je název sekundární síťové rozhraní:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Další postup
Kontrola [velikosti virtuálního počítače s Linuxem](sizes.md) při pokusu o vytvoření virtuálního počítače s několika síťovými kartami. Věnujte pozornost maximální počet síťových adaptérů podporuje všechny velikosti virtuálních počítačů.

Na další zabezpečení vašich virtuálních počítačů použít dočasný přístup virtuálních počítačů v. Tato funkce se otevře pravidla skupiny zabezpečení sítě pro provoz SSH, pokud je nepotřebujete a definovaného časového období. Další informace najdete v tématu popisujícím [správu přístupu k virtuálním počítačům pomocí metody právě včas](../../security-center/security-center-just-in-time.md).
