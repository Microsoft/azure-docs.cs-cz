---
title: Vytvoření virtuální sítě Azure s několika podsítěmi - rozhraní příkazového řádku Azure | Microsoft Docs
description: Naučte se vytvořit virtuální síť s více podsítěmi pomocí rozhraní příkazového řádku Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 0b0bfae02147910d98231d7c93f5ab260f26364f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880588"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-cli"></a>Vytvoření virtuální sítě s několika podsítěmi pomocí rozhraní příkazového řádku Azure

Virtuální síť umožňuje několik typů prostředků Azure pro komunikaci s Internetem a soukromě mezi sebou. Vytvoření více podsítí ve virtuální síti umožňuje segmentovat vaší síti, takže můžete filtrovat nebo řízení toku provozu mezi podsítěmi. V tomto článku zjistíte, jak:

> [!div class="checklist"]
> * Vytvoření virtuální sítě
> * Vytvoření podsítě
> * Testování síťovou komunikaci mezi virtuální počítače

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli.md). 

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork* s předponou adresy *10.0.0.0/16*. Příkaz vytvoří jednu podsíť s názvem *veřejné*, s předponou adresy *10.0.0.0/24*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Vzhledem k tomu, že v předchozí příkaz nebyl zadán do umístění, Azure vytvoří virtuální sítě ve stejném umístění, která *myResourceGroup* v skupiny prostředků existuje. **Předpony adres** a **předponu podsítě** jsou zadat v notaci CIDR. Předpona zadaná adresa patří 10.0.0.0-10.0.255.254 adresy IP. Předpona zadaná pro podsíť musí být v rámci předpona adresy definované pro virtuální síť. Azure DHCP přiřadí z adres předpony podsítě IP adres v podsíti nejsou nasazené prostředky. Azure přiřadí prostředky nasazené v rámci pouze adresy 10.0.0.4-10.0.0.254 **veřejné** podsíť, protože Azure si vyhrazuje první čtyři adres (10.0.0.0-10.0.0.3 podsítě, v tomto příkladu) a poslední adres ( 10.0.0.255 podsítě, v tomto příkladu) v každé podsíti.

## <a name="create-a-subnet"></a>Vytvoření podsítě

Vytvořte podsíť s [az sítě vnet podsíť vytváření](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Následující příklad vytvoří podsíť s názvem *privátní* v rámci *myVirtualNetwork* virtuální sítě s předponou adresy *10.0.1.0/24*. Předpona adresy musí být v rámci předpona adresy definované pro virtuální síť a předponu adresy jiných podsítí ve virtuální síti se nesmí překrývat.

```azurecli-interactive 
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24
```

Před nasazením virtuálních sítí Azure a podsítě pro použití v provozním prostředí, se doporučuje, že jste důkladně Seznamte se s adresním prostorem [aspekty](manage-virtual-network.md#create-a-virtual-network) a [limity virtuální síťové](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Jakmile se prostředky nasadí do podsítí, může vyžadovat některé virtuální síť a podsíť změny, jako je například změna rozsahy adres, opakované nasazení existující prostředky Azure, které jsou nasazeny v rámci podsítě.

## <a name="test-network-communication"></a>Test síťové komunikace

Virtuální síť umožňuje několik typů prostředků Azure pro komunikaci s Internetem a soukromě mezi sebou. Virtuální počítač je jeden typ prostředku, který můžete nasadit do virtuální sítě. Vytvořte dva virtuální počítače ve virtuální síti, abyste je mohli otestovat síťovou komunikaci mezi nimi a Internetu v pozdější fázi.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvoření virtuálního počítače s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVmWeb* v *veřejné* podsítě. `--no-wait` Parametr povoluje Azure a spusťte příkaz na pozadí, abyste mohli pokračovat na další příkaz. Pro zjednodušení tento kurz, je použít heslo. Klíče jsou obvykle používány v nasazení v produkčním prostředí. Pokud chcete použít klíče, je nutné také nakonfigurovat dokončit zbývající kroky přesměrování agenta SSH. Další informace o přesměrování agenta SSH naleznete v dokumentaci pro vašeho klienta SSH. Nahraďte `<replace-with-your-password>` v následujícím příkazu heslem dle vlastního výběru.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

10.0.0.4 Azure přiřadí automaticky jako privátní IP adresu virtuálního počítače, protože 10.0.0.4 je první dostupná IP adresa v *veřejné* podsítě. 

Vytvoření virtuálního počítače v *privátní* podsítě.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```
Virtuální počítač trvá několik minut pro vytvoření. Po vytvoření virtuálního počítače Azure CLI vrátí výstup podobně jako v následujícím příkladu: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Ve výstupu v příkladu můžete vidět, že **privateIpAddress** je *10.0.1.4*. Azure vytvořili [síťové rozhraní](virtual-network-network-interface.md), připojený k virtuálnímu počítači, síťové rozhraní mu přiřazená privátní adresa IP a **macAddress**. Azure DHCP automaticky přiřadí 10.0.1.4 na síťové rozhraní, protože se první dostupná IP adresa v *privátní* podsítě. Privátní IP a MAC adres zůstanou zařazené do síťového rozhraní, dokud je neodstraní síťové rozhraní. 

Poznamenejte si **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu do pozdějšího kroku. Když virtuální počítač není potřeba mít přiřazené veřejnou IP adresu, Azure přiřadí veřejnou IP adresu pro každý virtuální počítač, který vytvoříte, ve výchozím nastavení. K virtuálnímu počítači komunikovat z Internetu, musí mít veřejnou IP adresu přiřazenou k virtuálnímu počítači. Všechny virtuální počítače mohou komunikovat odchozí přes Internet, zda je k virtuálnímu počítači přiřazena veřejnou IP adresu. Další informace o odchozí připojení k Internetu v Azure najdete v tématu [odchozí připojení v Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuální počítače vytvořené v tomto článku mít jeden [síťové rozhraní](virtual-network-network-interface.md) s jednu IP adresu, která se dynamicky přiřadit síťové rozhraní. Poté, co nasadíte virtuální počítač, můžete [přidat víc veřejných a privátních IP adres, nebo změnit způsob přiřazení IP adresy statické](virtual-network-network-interface-addresses.md#add-ip-addresses). Můžete [přidat síťových rozhraní](virtual-network-network-interface-vm.md#vm-add-nic), až do limitu nepodporuje [velikost virtuálního počítače](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vyberete při vytváření virtuálního počítače. Můžete také [povolit single-root I/O virtualization (SR-IOV)](create-vm-accelerated-networking-cli.md) virtuálních počítačů, ale pouze tehdy, když vytvoření virtuálního počítače s velikostí virtuálních počítačů, která podporuje možnost.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Komunikace mezi virtuálními počítači a s Internetem

Použijte následující příkaz pro vytvoření relace SSH s *myVmMgmt* virtuálního počítače. Nahraďte `<publicIpAddress>` s veřejnou IP adresu virtuálního počítače. V předchozím příkladu veřejná IP adresa je *13.90.242.231*. Po zobrazení výzvy k zadání hesla, zadejte heslo zadané v [vytváření virtuálních počítačů](#create-virtual-machines).

```bash 
ssh azureuser@<publicIpAddress>
```

Z bezpečnostních důvodů je běžné omezit počet virtuálních počítačů, které můžete vzdáleně připojit k ve virtuální síti. V tomto kurzu *myVmMgmt* virtuální počítač se používá ke správě *myVmWeb* virtuálního počítače ve virtuální síti. Pomocí následujícího příkazu SSH k *myVmWeb* virtuální počítač z *myVmMgmt* virtuálního počítače:

```bash 
ssh azureuser@myVmWeb
```

Pro komunikaci se *myVmMgmt* virtuální počítač z *myVmWeb* virtuální počítač, zadejte následující příkaz z příkazového řádku:

```
ping -c 4 myvmmgmt
```

Zobrazí se výstup podobný výstupu v následujícím příkladu:
    
```
PING myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net (10.0.1.4) 56(84) bytes of data.
64 bytes from 10.0.1.4: icmp_seq=1 ttl=64 time=1.45 ms
64 bytes from 10.0.1.4: icmp_seq=2 ttl=64 time=0.628 ms
64 bytes from 10.0.1.4: icmp_seq=3 ttl=64 time=0.529 ms
64 bytes from 10.0.1.4: icmp_seq=4 ttl=64 time=0.674 ms

--- myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3029ms
rtt min/avg/max/mdev = 0.529/0.821/1.453/0.368 ms
```
      
Uvidíte, že adresa *myVmMgmt* 10.0.1.4 je virtuální počítač. 10.0.1.4 se první dostupná IP adresa v rozsahu adres *privátní* podsítě, které jste nasadili *myVmMgmt* virtuálního počítače v předchozím kroku.  Zjistíte, že plně kvalifikovaný název domény virtuálního počítače je *myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net*. I když *hxehizax3z1udjnrx1r4gr30pg* část názvu domény se liší pro virtuální počítač, zbývající části názvu domény jsou stejné. Ve výchozím nastavení používají všechny virtuální počítače Azure výchozí služba Azure DNS. Všechny virtuální počítače v rámci virtuální sítě můžete vyřešit názvy všechny ostatní virtuální počítače ve stejné virtuální síti pomocí služby DNS Azure, je výchozí. Místo použití služba DNS výchozí Azure, můžete použít serveru DNS nebo funkcí privátní domény služby Azure DNS. Podrobnosti najdete v tématu [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) nebo [pomocí Azure DNS pro domény privátní](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Použijte následující příkazy pro instalaci webového serveru nginx na *myVmWeb* virtuálního počítače:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Po instalaci nginx zavřete *myVmWeb* relace SSH, které vám zůstane na řádku *myVmMgmt* virtuálního počítače. Zadejte následující příkaz pro načtení na úvodní obrazovce nginx z *myVmWeb* virtuálního počítače.

```bash
curl myVmWeb
```

Na úvodní obrazovce nginx je vrácen.

Ukončení relace SSH s *myVmMgmt* virtuálního počítače.

Vytvoření Azure *myVmWeb* virtuálního počítače, na veřejnou IP adresu s názvem *myVmWebPublicIP* také vytvořil a přiřazené k virtuálnímu počítači. Získat veřejnou adresu Azure přiřazeny [az sítě veřejné ip zobrazit](/cli/azure/network/public-ip#az_network_public_ip_show).

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myVmWebPublicIP \
  --query ipAddress
```

Ze svého počítače, zadejte následující příkaz, nahraďte `<publicIpAddress>` s adresou vrácená z předchozí příkaz:

```bash
curl <publicIpAddress>
```

Pokus o curl na úvodní obrazovce nginx ze svého počítače selže. Pokus selže, protože když byly nasazené virtuální počítače, Azure vytvoří skupinu zabezpečení sítě pro každý virtuální počítač, ve výchozím nastavení. 

Skupina zabezpečení sítě obsahuje pravidla zabezpečení, která povolují nebo odepírají příchozí a odchozí síťový provoz pomocí portu a IP adresy. Výchozí skupina zabezpečení sítě, který vytvořili Azure umožňuje komunikaci přes všechny porty mezi prostředky ve stejné virtuální síti. Pro virtuální počítače s Linuxem výchozí skupinu zabezpečení sítě odmítne veškerý příchozí provoz z Internetu přes všechny porty, přijměte port TCP 22 (SSH). V důsledku toho ve výchozím nastavení, můžete také vytvořit relace SSH přímo na *myVmWeb* virtuálního počítače z Internetu, i když není vhodné port 22 otevřete na webový server. Vzhledem k tomu `curl` příkaz komunikuje přes port 80 a komunikace selže z Internetu vzhledem k tomu, že žádné pravidlo je ve skupině zabezpečení výchozí sítě umožňuje provoz přes port 80.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, použijte [odstranění skupiny az](/cli/azure/group#az_group_delete) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak nasadíte virtuální síť s více podsítěmi. Také jste zjistili, že když vytvoříte virtuální počítač s Linuxem, Azure vytvoří rozhraní sítě, připojí k virtuálnímu počítači a vytvoří skupinu zabezpečení sítě, který umožňuje pouze provoz přes port 22, z Internetu. Přechodu na dalším kurzu se dozvíte, jak filtrovat síťový provoz do podsítí, nikoli pro jednotlivé virtuální počítače.

> [!div class="nextstepaction"]
> [Filtr síťový provoz do podsítí](./virtual-networks-create-nsg-arm-cli.md)
