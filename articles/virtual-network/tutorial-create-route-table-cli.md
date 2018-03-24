---
title: Směrování síťového provozu - rozhraní příkazového řádku Azure | Microsoft Docs
description: Zjistěte, jak ke směrování síťového provozu s směrovací tabulku pomocí rozhraní příkazového řádku Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 871b562fa12b93d1b65e23ca58615d35ef6bb34b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Směrovat síťový provoz s směrovací tabulku pomocí rozhraní příkazového řádku Azure

Azure automaticky trasy provoz mezi všech podsítí v rámci virtuální sítě, ve výchozím nastavení. Můžete vytvořit vlastní trasy k přepsání Azure výchozí směrování. Možnost vytvářet vlastní trasy je užitečné, pokud například chcete směrovat přenos mezi podsítěmi přes virtuální síťové zařízení (hodnocení chyb zabezpečení). V tomto článku zjistíte, jak:

> [!div class="checklist"]
> * Vytvořit směrovací tabulku
> * Vytvořit trasu
> * Vytvoření virtuální sítě s několika podsítěmi
> * Přidružení tabulku směrování pro podsíť
> * Vytvoření hodnocení chyb zabezpečení, který směruje provoz
> * Nasazení virtuálních počítačů (VM) do různých podsítí
> * Směrovat provoz z jedné podsítě do jiné prostřednictvím hodnocení chyb zabezpečení

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, vyžaduje tento rychlý start, že používáte Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Vytvořit směrovací tabulku

Než bude možné vytvořit směrovací tabulku, vytvořte skupinu prostředků s [vytvořit skupinu az](/cli/azure/group#az_group_create) pro všechny prostředky, které jsou vytvořené v tomto článku. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

Vytvořit směrovací tabulku s [vytvoření tabulky trasy sítě az](/cli/azure/network/route#az_network_route_table_create). Následující příklad vytvoří směrovací tabulku s názvem *myRouteTablePublic*. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Vytvořit trasu

Vytvořit trasu ve směrovací tabulce s [az síťovou směrovací tabulku trasu vytvořit](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení tabulku směrování pro podsíť

Než budete moct přidružit tabulku směrování pro podsíť, budete muset vytvořit virtuální síť a podsíť. Vytvořit virtuální síť s jednou podsítí s [vytvoření sítě vnet az](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Vytvořit dva další podsítě s [az sítě vnet podsíť vytváření](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

Přidružení *myRouteTablePublic* směrovací tabulku, aby *veřejné* podsíť s [aktualizace az sítě vnet podsíť](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Vytvoření hodnocení chyb zabezpečení

Hodnocení chyb zabezpečení sítě je virtuální počítač, který provádí síťové funkce, například směrování, fungující brána firewall může nebo optimalizace sítě WAN.

Vytvoření hodnocení chyb zabezpečení sítě v *DMZ* podsíť s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create). Když vytvoříte virtuální počítač, Azure vytvoří a přiřadí veřejnou IP adresu virtuálního počítače, ve výchozím nastavení. `--public-ip-address ""` Parametr dá pokyn Azure není můžete vytvořit a přiřadit veřejnou IP adresu pro virtuální počítač, protože virtuální počítač nemusí být připojen k z Internetu. Pokud se klíče SSH již neexistují v umístění klíče výchozí, vytvoří příkaz je. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

Virtuální počítač trvá několik minut pro vytvoření. Nebudete pokračovat k dalšímu kroku až Azure dokončí vytváření virtuálního počítače a vrátí výstup o virtuálním počítači. 

Pro síťové rozhraní, abyste mohli předávat síťové přenosy odesílané do, který není určený pro vlastní IP adresu, musí být povolené předávání IP pro síťové rozhraní. Povolení předávání IP pro síťové rozhraní s [aktualizace seskupování sítě az](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

V rámci virtuálního počítače operační systém nebo aplikace běžící v rámci virtuálního počítače, musí taky umět předávat síťový provoz. Povolení předávání IP v operačním systému Virtuálního počítače s [nastavení rozšíření virtuálního az](/cli/azure/vm/extension#az_vm_extension_set):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
Příkaz může trvat až několik minut provést.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte dva virtuální počítače ve virtuální síti, abyste mohli ověřit, že provoz z *veřejné* podsítě se směruje na *privátní* podsítě hodnocení chyb zabezpečení v pozdější fázi. 

Vytvoření virtuálního počítače v *veřejné* podsíť s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create). `--no-wait` Parametr povoluje Azure a spusťte příkaz na pozadí, abyste mohli pokračovat na další příkaz. K zjednodušení tohoto článku, je použít heslo. Klíče jsou obvykle používány v nasazení v produkčním prostředí. Pokud chcete použít klíče, je nutné také nakonfigurovat přesměrování agenta SSH. Další informace naleznete v dokumentaci pro vašeho klienta SSH. Nahraďte `<replace-with-your-password>` v následujícím příkazu heslem dle vlastního výběru.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Vytvoření virtuálního počítače v *privátní* podsítě.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Virtuální počítač trvá několik minut pro vytvoření. Po vytvoření virtuálního počítače Azure CLI uvádí informace podobně jako v následujícím příkladu: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
Poznamenejte si **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu do pozdějšího kroku.

## <a name="route-traffic-through-an-nva"></a>Směrovat provoz prostřednictvím hodnocení chyb zabezpečení

Použijte následující příkaz pro vytvoření relace SSH s *myVmPrivate* virtuálních počítačů. Nahraďte *<publicIpAddress>* s veřejnou IP adresu vašeho virtuálního počítače. V příkladu nahoře, IP adresa je *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Po zobrazení výzvy k zadání hesla, zadejte heslo, které jste vybrali v [vytváření virtuálních počítačů](#create-virtual-machines).

Použijte následující příkaz k instalaci traceroute na *myVmPrivate* virtuálních počítačů:

```bash 
sudo apt-get install traceroute
```

Použijte následující příkaz k testování směrování pro síťový provoz *myVmPublic* virtuální počítač z *myVmPrivate* virtuálních počítačů.

```bash
traceroute myVmPublic
```

Odpověď je stejný jako v následujícím příkladu:

```bash
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Uvidíte, že provoz se směruje přímo z *myVmPrivate* virtuálního počítače *myVmPublic* virtuálních počítačů. Azure výchozích tras, směrovat provoz přímo mezi podsítěmi. 

Pomocí následujícího příkazu SSH k *myVmPublic* virtuální počítač z *myVmPrivate* virtuálních počítačů:

```bash 
ssh azureuser@myVmPublic
```

Použijte následující příkaz k instalaci traceroute na *myVmPublic* virtuálních počítačů:

```bash 
sudo apt-get install traceroute
```

Použijte následující příkaz k testování směrování pro síťový provoz *myVmPrivate* virtuální počítač z *myVmPublic* virtuálních počítačů.

```bash
traceroute myVmPrivate
```

Odpověď je stejný jako v následujícím příkladu:

```bash
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Uvidíte, že prvním skoku je 10.0.2.4, který je hodnocení chyb zabezpečení privátní IP adresu. Druhé směrování je 10.0.1.4, privátní IP adresu *myVmPrivate* virtuálních počítačů. Trasy přidat do *myRouteTablePublic* směrovací tabulky a přidružené k *veřejné* podsíť způsobila Azure pro směrování provozu prostřednictvím hodnocení chyb zabezpečení, nikoli přímo do *privátní* podsítě.

Zavřete relace SSH do obou *myVmPublic* a *myVmPrivate* virtuálních počítačů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, použijte [odstranění skupiny az](/cli/azure/group#az_group_delete) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

V tomto článku vytvořit směrovací tabulku a přidružené k podsíti. Můžete vytvořit jednoduché chyb zabezpečení, který směruje provoz z veřejné podsítě privátní podsítě. Nasazení celou řadu předem nakonfigurovaná NVAs, které provádějí síťových funkcí, jako jsou brány firewall a optimalizace sítě WAN z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Před nasazením směrovací tabulky pro použití v provozním prostředí, doporučujeme, aby důkladně Seznamte se s [směrování v Azure](virtual-networks-udr-overview.md), [spravovat směrovací tabulky](manage-route-table.md), a [Azure omezuje](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Když nasadíte mnoho prostředků Azure v rámci virtuální sítě, zdrojů u některých služeb Azure PaaS nelze nasadit do virtuální sítě. Můžete dál omezit přístup k prostředkům některé služby Azure PaaS provoz jenom z podsítě virtuální sítě, když. Přechodu na v dalším kurzu se dozvíte, jak omezit přístup k síti na Azure PaaS prostředky.

> [!div class="nextstepaction"]
> [Omezit přístup k síti na PaaS prostředky](tutorial-restrict-network-access-to-resources-cli.md)
