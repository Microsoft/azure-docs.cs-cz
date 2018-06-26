---
title: Použít interní DNS pro překlad názvů virtuálních počítačů s 2.0 rozhraní příkazového řádku Azure | Microsoft Docs
description: Jak vytvořit virtuální síťové adaptéry a používat interní DNS pro překlad názvů virtuálních počítačů v Azure pomocí Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/16/2017
ms.author: v-livech
ms.openlocfilehash: c1ca250d7255877cc811bf7c03034ecbb8f1f372
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936900"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Vytvořit virtuální síťové karty a používat interní DNS pro překlad názvů virtuálních počítačů v Azure
Tento článek ukazuje, jak nastavit statické interní názvy DNS pro virtuální počítače s Linuxem pomocí Azure CLI 2.0 virtuální síťové karty (vNics) a štítek názvy DNS. Statické názvy DNS jsou použity pro trvalé infrastruktury služby jako server volaných sestavení, který se používá k tomuto dokumentu nebo Git serveru.

Požadavky:

* [Účet Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Soubory veřejného a privátního klíče SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Rychlé příkazy
Pokud budete potřebovat rychle dosáhnout, v následující části jsou příkazy potřebné. Podrobnější informace a kontext pro každý krok naleznete ve zbývající části dokumentu [od zde](#detailed-walkthrough). K provedení těchto kroků, budete potřebovat nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login).

Předběžných požadavků: Skupinu prostředků, virtuální síť a podsíť, skupinu zabezpečení sítě pomocí protokolu SSH příchozí.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Vytvořit virtuální síťový adaptér se statickou interní název DNS
Vytvoření vNic s [vytvořit síťových adaptérů sítě az](/cli/azure/network/nic#az_network_nic_create). `--internal-dns-name` Příznak rozhraní příkazového řádku je k nastavení DNS popisku, který poskytuje statické název DNS pro virtuální síťové karty (vNic). Následující příklad vytvoří adaptéru vNic s názvem `myNic`, připojí se k `myVnet` virtuální síti a vytvoří interní záznam název DNS názvem `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Nasadit virtuální počítač a připojte se adaptéru vNic
Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). `--nics` Příznak během nasazení do Azure adaptér vNic připojuje k virtuálnímu počítači. Následující příklad vytvoří virtuální počítač s názvem `myVM` s Azure spravované disky a připojí adaptér vNic s názvem `myNic` z předchozího kroku:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>Podrobný postup

Úplné průběžnou integraci a průběžné nasazování (CiCd) vyžaduje určitých serverech být statické nebo dlohotrvající servery infrastruktury v Azure. Doporučujeme, aby Azure prostředky jako virtuální sítě a skupiny zabezpečení sítě jsou statické a dlouhodobě prostředky, které jsou nasazeny zřídka. Po nasazený virtuální síť, můžete použít znovu nová nasazení bez žádné negativní ovlivňuje infrastruktury. Později můžete přidat server úložiště Git nebo automatizační server volaných přináší CiCd do této virtuální sítě pro vývoj nebo testovací prostředí.  

Interní názvy DNS jsou pouze přeložit uvnitř virtuální sítě Azure. Protože se jedná o interní názvy DNS, nejsou přeložit na mimo Internetu, poskytuje dodatečné zabezpečení infrastruktury.

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Zahrnout názvy parametrů příklad `myResourceGroup`, `myNic`, a `myVM`.

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků
Nejprve vytvořte skupinu prostředků s [vytvořit skupinu az](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v `westus` umístění:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

Dalším krokem je vytvoření virtuální sítě pro spuštění virtuálních počítačů do. Virtuální síť obsahuje jednu podsíť pro účely tohoto postupu. Další informace o virtuálních sítí Azure najdete v tématu [vytvořit virtuální síť](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). 

Vytvoření virtuální sítě s [vytvoření sítě vnet az](/cli/azure/network/vnet#az_network_vnet_create). Následující příklad vytvoří virtuální síť s názvem `myVnet` a podsíť s názvem `mySubnet`:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Vytvořit skupinu zabezpečení sítě
Skupiny zabezpečení Azure sítě jsou ekvivalentní brány firewall síťové vrstvy. Další informace o skupinách zabezpečení sítě najdete v tématu [vytvoření skupin Nsg v Azure CLI](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Vytvořit skupinu zabezpečení sítě s [vytvořit az sítě nsg](/cli/azure/network/nsg#az_network_nsg_create). Následující příklad vytvoří skupinu zabezpečení sítě s názvem `myNetworkSecurityGroup`:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Přidat příchozí pravidlo umožňující SSH
Přidat příchozí pravidlo pro skupinu zabezpečení sítě s [vytvořit pravidla nsg sítě az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Následující příklad vytvoří pravidlo s názvem `myRuleAllowSSH`:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>Podsíť přidružit skupinu zabezpečení sítě
Chcete-li přidružit podsítě skupinu zabezpečení sítě, použijte [aktualizace az sítě vnet podsíť](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). Následující příklad přiřadí název podsítě `mySubnet` ke skupině zabezpečení sítě s názvem `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Vytvořit virtuální síťovou kartu a statické názvy DNS
Azure je velmi flexibilní, ale pokud chcete použít názvy DNS pro rozlišení názvu virtuálního počítače, budete muset vytvořit virtuální síťové adaptéry (vNics), které obsahují název DNS. vNics jsou důležité, protože můžete znovu použít, je jejich připojením do různých virtuálních počítačů přes životního cyklu infrastruktury. Tento přístup zajišťuje vNic jako statické prostředek při virtuálních počítačů může být dočasné. Pomocí DNS označování na kartě vNic jsou povolit jednoduchý překladu názvů z jiných virtuálních počítačů ve virtuální síti. Použití přeložit názvy umožňuje ostatním virtuálním počítačům přístup k serveru automatizace podle názvu DNS `Jenkins` nebo server Git jako `gitrepo`.  

Vytvoření vNic s [vytvořit síťových adaptérů sítě az](/cli/azure/network/nic#az_network_nic_create). Následující příklad vytvoří adaptéru vNic s názvem `myNic`, připojí se k `myVnet` virtuální síť s názvem `myVnet`a vytvoří interní záznam název DNS názvem `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Virtuální počítač nasaďte do infrastruktury virtuální sítě
Máme teď virtuální síť a podsíť, skupinu zabezpečení sítě funguje jako brána firewall blokuje veškerý příchozí provoz, s výjimkou port 22 pro SSH a adaptéru vNic chránit naše podsítě. Teď můžete nasadit virtuální počítač uvnitř této stávající síťové infrastruktuře.

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem `myVM` s Azure spravované disky a připojí adaptér vNic s názvem `myNic` z předchozího kroku:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Pomocí rozhraní příkazového řádku příznaky vyvolávající existující prostředky jsme vyzvat Azure k nasazení virtuálních počítačů uvnitř existující síť. Chcete-li zopakovat, jakmile nasazených virtuálních sítí a podsítí, může být ponecháno jako statické nebo trvalé prostředky uvnitř vaší oblasti Azure.  

## <a name="next-steps"></a>Další postup
* [Přímé vytvoření vlastního prostředí pro virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure CLI](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Vytvoření virtuálního počítače s Linuxem v Azure pomocí šablony](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
