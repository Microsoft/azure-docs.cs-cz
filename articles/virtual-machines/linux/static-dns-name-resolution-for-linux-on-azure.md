---
title: Použití interního serveru DNS pro překlad názvů virtuálních počítačů pomocí Azure CLI
description: Postup vytvoření karet virtuálních síťových rozhraní a použití interního serveru DNS pro překlad názvů virtuálních počítačů v Azure pomocí rozhraní příkazového řádku Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/16/2017
ms.author: cynthn
ms.openlocfilehash: 3d68ac7aa9927e62011c58b17139d7232ce4a10c
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200749"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Vytvoření síťových karet a použití interního serveru DNS pro překlad názvů virtuálních počítačů v Azure

V tomto článku se dozvíte, jak nastavit statické interní názvy DNS pro virtuální počítače se systémem Linux pomocí virtuálních síťových karet (virtuální síťové adaptéry) a názvů popisků DNS pomocí rozhraní příkazového řádku Azure CLI. Statické názvy DNS se používají pro trvalé služby infrastruktury, jako je Jenkinse sestavovací Server, který se používá pro tento dokument, nebo pro server Git.

Požadavky:

* [účet Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Soubory veřejného a privátního klíče SSH](mac-create-ssh-keys.md)

## <a name="quick-commands"></a>Rychlé příkazy
Pokud potřebujete úkol rychle provést, v následující části najdete podrobné informace o požadovaných příkazech. Podrobnější informace a kontext pro každý krok najdete ve zbývající části dokumentu, [začněte tady](#detailed-walkthrough). K provedení těchto kroků potřebujete mít nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlásili se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).

Předběžné požadavky: Skupina prostředků, virtuální síť a podsíť, skupina zabezpečení sítě s příchozím protokolem SSH.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Vytvoří virtuální síťovou kartu se statickým interním názvem DNS.
Vytvořte vNic pomocí [AZ Network nic Create](/cli/azure/network/nic). Příznak rozhraní příkazového `--internal-dns-name` řádku slouží k nastavení popisku DNS, který poskytuje statický název DNS pro virtuální síťovou kartu (vNic). Následující příklad vytvoří vNic s názvem `myNic` , připojí ho k `myVnet` virtuální síti a vytvoří interní záznam DNS s názvem `jenkins` :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Nasazení virtuálního počítače a připojení vNic
Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). `--nics`Příznak připojuje vNic k virtuálnímu počítači během nasazování do Azure. Následující příklad vytvoří virtuální počítač s názvem `myVM` s Azure Managed disks a připojí vNic s názvem `myNic` z předchozího kroku:

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

Úplná infrastruktura průběžné integrace a průběžného nasazování (CiCd) v Azure vyžaduje, aby některé servery byly statické nebo dlouhodobé servery. Doporučuje se, aby prostředky Azure, jako jsou virtuální sítě a skupiny zabezpečení sítě, byly statické a dlouhodobé nedlouhodobé prostředky, které se nasazují zřídka. Jakmile je virtuální síť nasazená, dá se znovu použít v nových nasazeních, aniž by to ovlivnilo žádnou nepříznivý vliv na infrastrukturu. Později můžete přidat server úložiště Git nebo server Jenkinse Automation, který poskytuje CiCd do této virtuální sítě pro vývojová nebo testovací prostředí.  

Interní názvy DNS se dá přeložit jenom v rámci služby Azure Virtual Network. Vzhledem k tomu, že názvy DNS jsou interní, nelze je přeložit na vnější Internet, což zajišťuje další zabezpečení infrastruktury.

V následujících příkladech nahraďte příklady názvů parametrů vlastními hodnotami. Příklady názvů parametrů zahrnují `myResourceGroup` , `myNic` , a `myVM` .

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků
Nejdřív vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků `myResourceGroup` v umístění `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

Dalším krokem je vytvoření virtuální sítě, do které se virtuální počítače spustí. Virtuální síť obsahuje jednu podsíť pro tento návod. Další informace o virtuálních sítích Azure najdete v tématu [vytvoření virtuální sítě](../../virtual-network/manage-virtual-network.md#create-a-virtual-network). 

Vytvořte virtuální síť pomocí [AZ Network VNet Create](/cli/azure/network/vnet). Následující příklad vytvoří virtuální síť s názvem `myVnet` a podsítí s názvem `mySubnet` :

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
Skupiny zabezpečení sítě Azure jsou ekvivalentní bráně firewall ve vrstvě sítě. Další informace o skupinách zabezpečení sítě najdete v tématu [Vytvoření skupin zabezpečení sítě v rozhraní příkazového řádku Azure](../../virtual-network/tutorial-filter-network-traffic-cli.md). 

Vytvořte skupinu zabezpečení sítě pomocí [AZ Network NSG Create](/cli/azure/network/nsg). Následující příklad vytvoří skupinu zabezpečení sítě s názvem `myNetworkSecurityGroup` :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Přidání příchozího pravidla pro povolení SSH
Přidejte příchozí pravidlo pro skupinu zabezpečení sítě pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule). Následující příklad vytvoří pravidlo s názvem `myRuleAllowSSH` :

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

## <a name="associate-the-subnet-with-the-network-security-group"></a>Přidružte podsíť ke skupině zabezpečení sítě.
K přidružení podsítě ke skupině zabezpečení sítě použijte [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet). Následující příklad přidruží název podsítě `mySubnet` ke skupině zabezpečení sítě s názvem `myNetworkSecurityGroup` :

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Vytvoření karty virtuální sítě a názvů statických DNS
Azure je velmi flexibilní, ale pokud chcete používat názvy DNS pro překlad názvů virtuálních počítačů, musíte vytvořit virtuální síťové karty (virtuální síťové adaptéry), které zahrnují popisek DNS. Virtuální síťové adaptéry jsou důležité, protože je můžete znovu použít tak, že je propojíte s různými virtuálními počítači přes životní cyklus infrastruktury. Tento přístup udržuje vNic jako statický prostředek, zatímco virtuální počítače můžou být dočasné. Díky označování DNS v vNic dokážeme povolit jednoduché překlad IP adres z jiných virtuálních počítačů ve virtuální síti. Použití přeložitelných názvů umožňuje ostatním virtuálním počítačům přistupovat k automatizačnímu serveru pomocí názvu DNS `Jenkins` nebo serveru Git jako `gitrepo` .  

Vytvořte vNic pomocí [AZ Network nic Create](/cli/azure/network/nic). Následující příklad vytvoří vNic s názvem `myNic` , připojí ho k `myVnet` virtuální síti s názvem `myVnet` a vytvoří interní záznam DNS s názvem `jenkins` :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Nasazení virtuálního počítače do infrastruktury virtuální sítě
Teď máme virtuální síť a podsíť, což je skupina zabezpečení sítě, která funguje jako brána firewall pro ochranu naší podsítě blokováním veškerého příchozího provozu s výjimkou portu 22 pro SSH a vNic. Virtuální počítač teď můžete nasadit v této stávající síťové infrastruktuře.

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Následující příklad vytvoří virtuální počítač s názvem `myVM` s Azure Managed disks a připojí vNic s názvem `myNic` z předchozího kroku:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Když použijete příznaky rozhraní příkazového řádku pro volání stávajících prostředků, podáváme Azure, aby virtuální počítač nasadil do stávající sítě. Aby se opakovala, po nasazení virtuální sítě a podsítě můžou být v rámci oblasti Azure ponechány jako statické nebo trvalé prostředky.  

## <a name="next-steps"></a>Další kroky
* [Přímé vytvoření vlastního prostředí pro virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure CLI](create-cli-complete.md).
* [Vytvoření virtuálního počítače se systémem Linux v Azure pomocí šablon](create-ssh-secured-vm-from-template.md)
