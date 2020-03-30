---
title: Vytvoření virtuálního počítače s Linuxem v Azure s více síťovémi aplikacemi
description: Zjistěte, jak vytvořit virtuální počítač s Linuxem s více připojenými síťovémi aplikacemi pomocí šablon Azure CLI nebo Resource Manager.
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: ecbff4beadd9d10a8489c89cc322c0bb67ec5f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267180"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Jak vytvořit virtuální počítač Linuxu v Azure s více kartami síťového rozhraní


Tento článek podrobně popisuje, jak vytvořit virtuální počítač s více síťové karty s Azure CLI.

## <a name="create-supporting-resources"></a>Vytvoření podpůrných prostředků
Nainstalujte nejnovější [azure cli](/cli/azure/install-az-cli2) a přihlaste se k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index).

V následujících příkladech nahraďte názvy příkladů parametrů vlastními hodnotami. Příklad y názvů parametrů zahrnovaly *myResourceGroup*, *mystorageaccount*a *myVM*.

Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální síť s [vytvořením virtuální sítě AZ](/cli/azure/network/vnet). Následující příklad vytvoří virtuální síť s názvem *myVnet* a podsíť s názvem *mySubnetFrontEndEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Vytvořte podsíť pro back-endový provoz s [vytvořením podsítě sítě AZ](/cli/azure/network/vnet/subnet). Následující příklad vytvoří podsíť s názvem *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Vytvořte skupinu zabezpečení sítě pomocí vytvoření [sítě az nsg](/cli/azure/network/nsg). Následující příklad vytvoří skupinu zabezpečení sítě *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Vytvoření a konfigurace více nicotnic
Vytvořte dvě síťové karty s [az network nic create](/cli/azure/network/nic). Následující příklad vytvoří dvě síťové karty s názvem *myNic1* a *myNic2*, které připojily skupinu zabezpečení sítě, přičemž k každé podsíti se připojí jedna síťová síť:

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

## <a name="create-a-vm-and-attach-the-nics"></a>Vytvoření virtuálního počítače a připojení nic
Při vytváření virtuálního počítače zadejte síťové `--nics`karty, které jste vytvořili pomocí . Musíte také dbát, když vyberete velikost virtuálního počítače. Existují limity pro celkový počet nic, které můžete přidat do virtuálního počítače. Přečtěte si další informace o [velikostech virtuálních počítačích v Linuxu](sizes.md).

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Následující příklad vytvoří virtuální hod s názvem *myVM*:

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

Přidejte směrovací tabulky do hostovaného operačního serveru provedením kroků v [části Konfigurace hostovaného operačního serveru pro více aplikací NIC](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Přidání nic k virtuálnímu virtuálnímu jevu
Předchozí kroky vytvořily virtuální počítač s více síťové karty. Síťové karty můžete také přidat do existujícího virtuálního počítače pomocí příkazového příkazového příkazu k Řešení Azure. Různé [velikosti virtuálních počítačů](sizes.md) podporují různý počet nic, takže velikost virtuálního počítače odpovídajícím způsobem. V případě potřeby můžete [změnit velikost virtuálního počítače](change-vm-size.md).

Vytvořte další síťovou síť ovou pomocí [az network nic create](/cli/azure/network/nic). Následující příklad vytvoří síťovou síť ovou síť s názvem *myNic3* připojenou ke skupině zabezpečení back-endové podsítě a sítě vytvořené v předchozích krocích:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Chcete-li přidat nic k existujícímu virtuálnímu virtuálnímu jevu, nejprve navrátit virtuální ho s [az vm navrátit](/cli/azure/vm). Následující příklad navrací virtuální hosti s názvem *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Přidejte NIC s [az vm nic přidat](/cli/azure/vm/nic). Následující příklad přidá *myNic3* *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Spuštění virtuálního virtuálního [virtuálního virtuálního montova se spuštěním virtuálního virtuálního virtuálního virtuálního virtuálního](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Přidejte směrovací tabulky do hostovaného operačního serveru provedením kroků v [části Konfigurace hostovaného operačního serveru pro více aplikací NIC](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Odebrání nic z virtuálního mísy
Chcete-li odebrat nic z existujícího virtuálního soudu, nejprve navrátit virtuální ho s [az vm navrátit](/cli/azure/vm). Následující příklad navrací virtuální hosti s názvem *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Vyjměte NIC s [az vm nic odstranit](/cli/azure/vm/nic). Následující příklad odebere *myNic3* z *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Spuštění virtuálního virtuálního [virtuálního virtuálního montova se spuštěním virtuálního virtuálního virtuálního virtuálního virtuálního](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Vytvoření více nic pomocí šablon Správce prostředků
Šablony Azure Resource Manageru používají k definování prostředí deklarativní soubory JSON. Můžete si přečíst [přehled Správce prostředků Azure](../../azure-resource-manager/management/overview.md). Šablony Správce prostředků poskytují způsob, jak vytvořit více instancí prostředku během nasazení, například vytvoření více nic. Pomocí *kopie* můžete určit počet instancí, které chcete vytvořit:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Přečtěte si další informace o [vytváření více instancí pomocí *kopírování*](../../resource-group-create-multiple.md). 

Můžete také použít `copyIndex()` k připojení čísla k názvu prostředku, který `myNic1`umožňuje `myNic2`vytvořit , atd. Následující příklad připojení hodnoty indexu:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Můžete si přečíst úplný příklad [vytvoření více nic pomocí šablony Správce prostředků](../../virtual-network/template-samples.md).

Přidejte směrovací tabulky do hostovaného operačního serveru provedením kroků v [části Konfigurace hostovaného operačního serveru pro více aplikací NIC](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurace hostovaného operačního serveru pro více nic

Předchozí kroky vytvořily virtuální síť a podsíť, připojily síťové karty a pak vytvořily virtuální počítač. Veřejná IP adresa a pravidla skupiny zabezpečení sítě, která umožňují provoz SSH, nebyly vytvořeny. Chcete-li nakonfigurovat hostovaný operační systém pro více síťových aplikací, musíte povolit vzdálená připojení a spouštět příkazy místně na virtuálním počítači.

Chcete-li povolit provoz SSH, vytvořte pravidlo skupiny zabezpečení sítě s [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) takto:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Vytvořte veřejnou IP adresu s [az sítě public-ip vytvořit](/cli/azure/network/public-ip#az-network-public-ip-create) a přiřadit ji k první NIC s [az sítě nic ip-config aktualizace](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Chcete-li zobrazit veřejnou IP adresu virtuálního počítačů, použijte [az vm show](/cli/azure/vm#az-vm-show) takto::

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Teď SSH na veřejnou IP adresu vašeho virtuálního počítače. Výchozí uživatelské jméno uvedené v předchozím kroku byl *azureuser*. Zadejte své vlastní uživatelské jméno a veřejnou IP adresu:

```bash
ssh azureuser@137.117.58.232
```

Chcete-li odeslat do nebo ze sekundárního síťového rozhraní, je nutné ručně přidat trvalé trasy do operačního systému pro každé sekundární síťové rozhraní. V tomto článku *eth1* je sekundární rozhraní. Pokyny pro přidání trvalých tras do operačního systému se liší podle distro. Pokyny naleznete v dokumentaci k distro.

Při přidávání trasy do operačního systému je adresa brány *0,1* pro kteroukoli podsíť, ve které se síťové rozhraní nachází. Pokud je například síťovému rozhraní přiřazena adresa *10.0.2.4*, je brána zadaná pro trasu *10.0.2.1*. Můžete definovat určitou síť pro cíl trasy nebo zadat cíl *0.0.0.0*, pokud chcete, aby veškerý provoz rozhraní procházel zadanou bránou. Brána pro každou podsíť je spravována virtuální sítí.

Po přidání trasy pro sekundární rozhraní ověřte, zda je trasa `route -n`v tabulce tras pomocí aplikace . Následující příklad výstupu je pro směrovací tabulku, která má dvě síťová rozhraní přidaná k virtuálnímu virtuálnímu zařízení v tomto článku:

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

Zkontrolujte, zda přidaná trasa přetrvává po restartování, a to tak, že po restartování znovu zkontrolujete směrovací tabulku. Chcete-li otestovat připojení, můžete zadat následující příkaz, například kde *eth1* je název sekundárního síťového rozhraní:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Další kroky
Zkontrolujte [velikosti virtuálních počítačů linuxu](sizes.md) při pokusu o vytvoření virtuálního počítače s více síťové karty. Věnujte pozornost maximálnímu počtu nic, které každá velikost virtuálního počítače podporuje.

Pokud chcete virtuální počítače ještě víc zabezpečit, použijte přístup k virtuálním počítačům právě včas. Tato funkce otevře pravidla skupiny zabezpečení sítě pro provoz SSH v případě potřeby a po definovanou dobu. Další informace najdete v tématu popisujícím [správu přístupu k virtuálním počítačům pomocí metody právě včas](../../security-center/security-center-just-in-time.md).
