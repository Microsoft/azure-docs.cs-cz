---
title: Použití interního serveru DNS pro překlad adres virtuálních počítačů pomocí Azure CLI | Dokumentace Microsoftu
description: Jak vytvořit virtuální síťové karty a použití interního serveru DNS pro překlad adres virtuálních počítačů v Azure pomocí Azure CLI
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
ms.openlocfilehash: 862d239227c277a92cbf80e54b010a4b184da016
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54466087"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Vytvořit virtuální síťové karty a použití interního serveru DNS pro překlad adres virtuálních počítačů v Azure

V tomto článku se dozvíte, jak nastavit statickou interní názvy DNS pro virtuální počítače s Linuxem pomocí virtuální síťové karty (Vnic) a názvy popisků DNS pomocí Azure CLI. Statické názvy DNS jsou použity pro trvalé infrastruktury služeb, jako je server sestavení Jenkinse, který se používá pro tento dokument nebo serveru Git.

Požadavky:

* [Účet Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Soubory veřejného a privátního klíče SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Rychlé příkazy
Pokud je potřeba rychle provést úlohu, následující část obsahuje podrobný příkazy potřebné. Podrobnější informace a kontext pro každý krok najdete ve zbývající části dokumentu [od tady](#detailed-walkthrough). Pokud chcete tento postup, musíte na nejnovější verzi [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index#az_login).

Předběžné požadavky: Skupina prostředků, virtuální síť a podsíť, skupiny zabezpečení sítě pomocí protokolu SSH příchozí.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Vytvoření virtuální síťové karty s statické interní název DNS
Vytvořit virtuální síťový adaptér s [az network nic vytvořit](/cli/azure/network/nic#az_network_nic_create). `--internal-dns-name` Příznak CLI je k nastavení názvu DNS, který poskytuje statické název DNS pro virtuální síťový adaptér (vNic). Následující příklad vytvoří virtuální síťový adaptér s názvem `myNic`, připojí se k `myVnet` virtuální sítě a vytvoří záznam interní název DNS názvem `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Nasazení virtuálního počítače a připojení virtuálního síťového adaptéru
Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). `--nics` Příznak připojí virtuálního síťového adaptéru k virtuálnímu počítači při nasazování do Azure. Následující příklad vytvoří virtuální počítač s názvem `myVM` s Azure Managed Disks a bude k obrazci virtuálního síťového adaptéru s názvem `myNic` z předchozího kroku:

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

Úplné průběžné integrace a průběžného nasazování (CiCd) infrastrukturu v Azure vyžaduje některé servery být statické nebo dlouhodobé servery. Doporučuje se, že Azure prostředky, jako jsou virtual networks a skupin zabezpečení sítě jsou statické a dlouhodobě zřídka nasazovaných prostředků. Po nasazení virtuální sítě, můžete použít opakovaně nová nasazení bez jakékoli negativní vliv infrastruktury. Později přidáte server úložiště Git nebo automatizační server Jenkins poskytuje CiCd do této virtuální sítě pro vaše vývojové nebo testovací prostředí.  

Interní názvy DNS jsou pouze Přeložitelné uvnitř virtuální sítě Azure. Protože se jedná o interní názvy DNS, nejsou přeložit na vnější internet, poskytuje dodatečné zabezpečení pro infrastrukturu.

V následujících příkladech nahraďte ukázkové názvy parametrů s vlastními hodnotami. Zahrnout názvy parametrů příklad `myResourceGroup`, `myNic`, a `myVM`.

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků
Nejprve vytvořte skupinu prostředků s [vytvořit skupiny az](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků `myResourceGroup` v umístění `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

Dalším krokem je vytvoření virtuální sítě pro spuštění virtuálních počítačů do. Virtuální síť obsahuje jednu podsíť v tomto návodu. Další informace o virtuálních sítích Azure najdete v tématu [vytvoření virtuální sítě](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). 

Vytvoření virtuální sítě s [az network vnet vytvořit](/cli/azure/network/vnet). Následující příklad vytvoří virtuální síť s názvem `myVnet` a podsíť s názvem `mySubnet`:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Vytvořit skupinu zabezpečení sítě
Azure skupiny zabezpečení sítě jsou ekvivalentní do brány firewall na síťové vrstvě. Další informace o skupinách zabezpečení sítě najdete v tématu [jak vytvořit skupiny Nsg v Azure CLI](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Vytvořit skupinu zabezpečení sítě s [az network nsg vytvořit](/cli/azure/network/nsg#az_network_nsg_create). Následující příklad vytvoří skupinu zabezpečení sítě s názvem `myNetworkSecurityGroup`:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Přidat pravidlo pro příchozí spojení umožňující SSH
Přidat příchozí pravidlo pro skupinu zabezpečení sítě s [az network nsg pravidlo vytvořte](/cli/azure/network/nsg/rule). Následující příklad vytvoří pravidlo `myRuleAllowSSH`:

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

## <a name="associate-the-subnet-with-the-network-security-group"></a>Přidružte skupinu zabezpečení sítě podsíť
Chcete-li přidružit podsíť skupinu zabezpečení sítě, použijte [az network vnet podsíť aktualizace](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). Následující příklad přiřadí název podsítě `mySubnet` se skupinou zabezpečení sítě s názvem `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Vytvořte virtuální síťové karty a statické názvy DNS
Azure je velmi flexibilní, ale použít názvy DNS pro překlad adres virtuálních počítačů, je potřeba vytvořit virtuální síťové adaptéry (Vnic), zahrnující názvu DNS. virtuální síťové adaptéry jsou důležité, protože můžete znovu použít, je jejich připojením do různých virtuálních počítačů přes životní cyklus infrastruktury. Tento přístup zajišťuje virtuálního síťového adaptéru jako statických prostředků, virtuální počítače mohou být dočasné. Pomocí označování na virtuálního síťového adaptéru DNS jsou možnost povolit jednoduchý název řešení z jiných virtuálních počítačů ve virtuální síti. Použití přeložit názvy umožňuje ostatním virtuálním počítačům přístup k serveru automatizace podle názvu DNS `Jenkins` nebo serveru Git jako `gitrepo`.  

Vytvořit virtuální síťový adaptér s [az network nic vytvořit](/cli/azure/network/nic#az_network_nic_create). Následující příklad vytvoří virtuální síťový adaptér s názvem `myNic`, připojí se k `myVnet` virtuální síť s názvem `myVnet`a vytvoří záznam interní název DNS názvem `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Nasazení virtuálního počítače do infrastruktury virtuální sítě
Teď máme virtuální síť a podsíť, skupiny zabezpečení sítě funguje jako brána firewall k ochraně našich podsítě zákonné zodpovědnosti organizací blokováním veškerý příchozí provoz, s výjimkou port 22 pro SSH a virtuální síťový adaptér. Teď můžete nasadit virtuální počítač se tento stávající síťové infrastruktuře.

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Následující příklad vytvoří virtuální počítač s názvem `myVM` s Azure Managed Disks a bude k obrazci virtuálního síťového adaptéru s názvem `myNic` z předchozího kroku:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Pomocí příznaků příkazového řádku a volejte stávající prostředky, můžeme dát pokyn Azure k nasazení virtuálních počítačů uvnitř stávající sítě. Zdůrazňujeme, jakmile jsou nasazené virtuální síť a podsíť, může být ponecháno jako statické nebo trvalý prostředků ve vaší oblasti Azure.  

## <a name="next-steps"></a>Další postup
* [Přímé vytvoření vlastního prostředí pro virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure CLI](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Vytvoření virtuálního počítače s Linuxem v Azure pomocí šablony](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
