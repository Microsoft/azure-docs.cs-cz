---
title: Použití interního překladu názvů virtuálních počítačů pro virtuální počítače s azure CLI
description: Jak vytvořit karty virtuálního síťového rozhraní a použít interní překlad názvů DNS pro virtuální počítače v Azure pomocí rozhraní příkazového řádku Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/16/2017
ms.author: cynthn
ms.openlocfilehash: 07a78e4987a844627824ac5034046cf6a393ad8d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757842"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Vytvoření karet rozhraní virtuální sítě a použití interního překladu názvů DNS pro virtuální počítače v Azure

Tento článek ukazuje, jak nastavit statické interní názvy DNS pro virtuální počítače s Linuxem pomocí virtuálních síťových karet rozhraní (vNics) a názvů popisků DNS pomocí rozhraní příkazového řádku Azure. Statické názvy DNS se používají pro trvalé infrastrukturní služby, jako je server jenkinsového sestavení, který se používá pro tento dokument, nebo server Git.

Požadavky:

* [účet Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Soubory veřejného a privátního klíče SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Rychlé příkazy
Pokud potřebujete rychle provést úkol, v následující části podrobnosti o potřebné příkazy. Podrobnější informace a kontext pro každý krok naleznete ve zbytku dokumentu, [počínaje zde](#detailed-walkthrough). K provedení těchto kroků potřebujete nejnovější [azure cli](/cli/azure/install-az-cli2) nainstalované a přihlášené k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index).

Předběžné požadavky: Skupina prostředků, virtuální síť a podsíť, Skupina zabezpečení sítě s příchozím SSH.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Vytvoření karty rozhraní virtuální sítě se statickým interním názvem DNS
Vytvořte vNic s [az sítě nic vytvořit](/cli/azure/network/nic). Příznak `--internal-dns-name` rozhraní se křižovatek je určen pro nastavení popisku DNS, který poskytuje statický název DNS pro kartu rozhraní virtuální sítě (vNic). Následující příklad vytvoří název `myNic`vNic s názvem `myVnet` , připojí jej k virtuální `jenkins`síti a vytvoří interní název DNS s názvem :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Nasazení virtuálního virtuálního uživatele a připojení vNic
Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Příznak `--nics` připojí vNic k virtuálnímu počítači během nasazení do Azure. Následující příklad vytvoří virtuální `myVM` počítač s názvem spravované disky Azure `myNic` a připojí vNic pojmenované z předchozího kroku:

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

Úplná průběžná integrace a průběžné nasazení (CiCd) infrastruktury v Azure vyžaduje, aby určité servery statické nebo dlouhodobé servery. Doporučuje se, aby prostředky Azure, jako jsou virtuální sítě a skupiny zabezpečení sítě, byly statické a dlouhodobé prostředky, které se jen zřídka nasazují. Jakmile je virtuální síť nasazena, může ji znovu použít nová nasazení bez jakéhokoli nepříznivého vlivu na infrastrukturu. Později můžete přidat server úložiště Git nebo server automatizace Jenkins doručuje CiCd do této virtuální sítě pro vývoj nebo testovací prostředí.  

Interní názvy DNS lze řešit jenom uvnitř virtuální sítě Azure. Vzhledem k tomu, že názvy DNS jsou interní, nelze je řešit na externí internet a poskytují další zabezpečení infrastruktury.

V následujících příkladech nahraďte názvy příkladů parametrů vlastními hodnotami. Příklady názvů `myResourceGroup` `myNic`parametrů `myVM`zahrnují , , a .

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků
Nejprve vytvořte skupinu prostředků pomocí [vytvoření skupiny az](/cli/azure/group). Následující příklad vytvoří skupinu prostředků `myResourceGroup` v umístění `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

Dalším krokem je vytvoření virtuální sítě pro spuštění virtuálních počítačích. Virtuální síť obsahuje jednu podsíť pro tento návod. Další informace o virtuálních sítích Azure najdete [v tématu Vytvoření virtuální sítě](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). 

Vytvořte virtuální síť s [vytvořením virtuální sítě AZ](/cli/azure/network/vnet). Následující příklad vytvoří virtuální `myVnet` síť s `mySubnet`názvem a podsíť s názvem :

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
Skupiny zabezpečení sítě Azure jsou ekvivalentní brány firewall v síťové vrstvě. Další informace o skupinách zabezpečení sítě najdete [v tématu Jak vytvořit skupiny zabezpečení sítě v nastavení příkazového příkazového příkazu k síti Azure](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Vytvořte skupinu zabezpečení sítě pomocí vytvoření [sítě az nsg](/cli/azure/network/nsg). Následující příklad vytvoří skupinu `myNetworkSecurityGroup`zabezpečení sítě s názvem :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Přidání příchozího pravidla pro povolení ssh
Přidejte příchozí pravidlo pro skupinu zabezpečení sítě pomocí [vytvoření pravidla az network nsg](/cli/azure/network/nsg/rule). Následující příklad vytvoří pravidlo `myRuleAllowSSH`s názvem :

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

## <a name="associate-the-subnet-with-the-network-security-group"></a>Přidružení podsítě ke skupině zabezpečení sítě
Chcete-li přidružit podsíť ke skupině zabezpečení sítě, použijte [aktualizaci podsítě sítě AZ](/cli/azure/network/vnet/subnet). Následující příklad přidruží `mySubnet` název podsítě ke `myNetworkSecurityGroup`skupině zabezpečení sítě s názvem :

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Vytvoření karty rozhraní virtuální sítě a statických názvů DNS
Azure je velmi flexibilní, ale pro překlad názvů virtuálních počítačů je potřeba vytvořit karty virtuálního síťového rozhraní (vNics), které obsahují popisek DNS. vNics jsou důležité, protože je můžete znovu použít jejich připojením k různým virtuálním virtuálním mům v průběhu životního cyklu infrastruktury. Tento přístup udržuje vNic jako statický prostředek, zatímco virtuální chod může být dočasné. Pomocí popisků DNS na virtuálním počítači jsme schopni povolit jednoduché překlady názvů z jiných virtuálních počítačů ve virtuální síti. Použití resolvable názvy umožňuje ostatním virtuálním počítačům `Jenkins` přístup k serveru `gitrepo`automatizace podle názvu DNS nebo serveru Git jako .  

Vytvořte vNic s [az sítě nic vytvořit](/cli/azure/network/nic). Následující příklad vytvoří název `myNic`vNic , připojí `myVnet` jej `myVnet`k virtuální síti s názvem `jenkins`a vytvoří interní název DNS s názvem :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Nasazení virtuálního počítače do infrastruktury virtuální sítě
Nyní máme virtuální síť a podsíť, skupinu zabezpečení sítě, která funguje jako brána firewall k ochraně naší podsítě tím, že blokuje všechny příchozí přenosy kromě portu 22 pro SSH a vNic. Teď můžete nasadit virtuální hovado uvnitř této existující síťové infrastruktury.

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Následující příklad vytvoří virtuální `myVM` počítač s názvem spravované disky Azure `myNic` a připojí vNic pojmenované z předchozího kroku:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Pomocí příznaků příkazového příkazového příkazu k volání existujících prostředků dáváme Pokyn Azure k nasazení virtuálního počítače v existující síti. Chcete-li zopakovat, jakmile virtuální síť a podsíť byly nasazeny, mohou být ponechány jako statické nebo trvalé prostředky uvnitř oblasti Azure.  

## <a name="next-steps"></a>Další kroky
* [Přímé vytvoření vlastního prostředí pro virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure CLI](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Vytvoření virtuálního počítače s Linuxem v Azure pomocí šablon](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
