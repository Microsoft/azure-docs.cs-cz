---
title: "Směrování síťového provozu - rozhraní příkazového řádku Azure | Microsoft Docs"
description: "Zjistěte, jak ke směrování síťového provozu s směrovací tabulku pomocí rozhraní příkazového řádku Azure."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/02/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 3c16c774fa1c8a5c8bf50b4f4f9d0bfb283318e3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Směrovat síťový provoz s směrovací tabulku pomocí rozhraní příkazového řádku Azure

Azure automaticky trasy provoz mezi všech podsítí v rámci virtuální sítě, ve výchozím nastavení. Můžete vytvořit vlastní trasy k přepsání Azure výchozí směrování. Možnost vytvářet vlastní trasy je užitečné, pokud například chcete směrovat přenos mezi podsítěmi přes bránu firewall. V tomto článku zjistíte, jak:

> [!div class="checklist"]
> * Vytvořit směrovací tabulku
> * Vytvořit trasu
> * Přidružení tabulku směrování pro podsíť virtuální sítě
> * Testování směrování
> * Řešení potíží s směrování

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, vyžaduje tento rychlý start, že používáte Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Vytvořit směrovací tabulku

Azure směrování provozu mezi všech podsítí ve virtuální síti, ve výchozím nastavení. Další informace o výchozích tras Azure najdete v tématu [systémové trasy](virtual-networks-udr-overview.md). Pokud chcete přepsat výchozí Azure a směrování, vytvořit směrovací tabulku, která obsahuje trasy a přidružit tabulku směrování pro podsíť virtuální sítě.

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

Směrovací tabulka obsahuje nula nebo víc tras. Vytvořit trasu ve směrovací tabulce s [az síťovou směrovací tabulku trasu vytvořit](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

Trasy, která bude směrovat všechny přenosy určené do předpona adresy 10.0.1.0/24 prostřednictvím sítě virtuálního zařízení s IP adresou 10.0.2.4. Virtuální zařízení sítě a podsítě s předponou zadaná adresa se vytvoří v dalších krocích. Trasy přepíše Azure výchozí směrování, který směruje provoz mezi podsítěmi přímo. Každý postup určuje typ dalšího směrování. Typ dalšího přechodu dá pokyn Azure směrování provozu. V tomto příkladu je typ dalšího přechodu *VirtualAppliance*. Další informace o všech dostupných další typy směrování v Azure a jejich použití naleznete v tématu [dalšího směrování typy](virtual-networks-udr-overview.md#custom-routes).

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

Můžete přidružit směrovací tabulku do nula nebo více podsítí. Podsíť může mít žádnou nebo jednu směrovací tabulku přidružené k němu. Odchozí provoz z podsítě se směruje na základě Azure výchozí trasy a jakékoli vlastní trasy, která jste přidali do tabulky směrování, kterou přidružíte k podsíti. Přidružení *myRouteTablePublic* směrovací tabulku, aby *veřejné* podsíť s [aktualizace az sítě vnet podsíť](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

Před nasazením směrovací tabulky pro použití v provozním prostředí, doporučujeme, aby důkladně Seznamte se s [směrování v Azure](virtual-networks-udr-overview.md) a [Azure omezení](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Testování směrování

K testování směrování, vytvoříte virtuální počítač, který slouží jako virtuální zařízení sítě, prostřednictvím směruje trasy, kterou jste vytvořili v předchozím kroku. Po vytvoření virtuálního zařízení sítě, budete nasazovat do virtuálního počítače *veřejné* a *privátní* podsítě. Budete pak směrovat provoz z *veřejné* podsítě, který *privátní* podsítě virtuální síťové zařízení.

### <a name="create-a-network-virtual-appliance"></a>Vytvořit virtuální síťové zařízení

V předchozím kroku jste vytvořili trasu, která zadaný virtuální zařízení sítě jako typ dalšího směrování. Virtuální počítač spuštěný síťové aplikace se často označuje jako virtuální zařízení sítě. V produkčním prostředí je virtuální zařízení sítě, které můžete nasadit často předem nakonfigurovaný virtuální počítač. Několik síťových virtuálních zařízení jsou k dispozici na [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). V tomto článku se vytvoří základní virtuální počítač. 

Vytvořit virtuální zařízení v síti *DMZ* podsíť s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create). Když vytvoříte virtuální počítač, Azure vytvoří a přiřadí veřejnou IP adresu virtuálního počítače, ve výchozím nastavení. `--public-ip-address ""` Parametr dá pokyn Azure není můžete vytvořit a přiřadit veřejnou IP adresu pro virtuální počítač, protože virtuální počítač nemusí být připojen k z Internetu. Pokud se klíče SSH již neexistují v umístění klíče výchozí, vytvoří příkaz je. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

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

Virtuální počítač trvá několik minut pro vytvoření. Nebudete pokračovat k dalšímu kroku až Azure dokončí vytváření virtuálního počítače a vrátí výstup o virtuálním počítači. V produkčním prostředí je virtuální zařízení sítě, které můžete nasadit často předem nakonfigurovaný virtuální počítač. Několik síťových virtuálních zařízení jsou k dispozici na [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1).

Je nutné povolit předávání IP pro každý Azure [síťové rozhraní](virtual-network-network-interface.md) připojen k virtuálnímu počítači, který předává provoz určený pro každou IP adresu, která není přiřazen k síťovému rozhraní. Pokud jste vytvořili virtuální síťové zařízení v *DMZ* podsíť, Azure automaticky vytvoří síťové rozhraní s názvem *myVmNvaVMNic*, připojit síťové rozhraní k virtuálnímu počítači, a Přiřazená privátní adresa IP *10.0.2.4* na síťové rozhraní. Povolení předávání IP pro síťové rozhraní s [aktualizace seskupování sítě az](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

V rámci virtuálního počítače operační systém nebo aplikace běžící v rámci virtuálního počítače, musí taky umět předávat síťový provoz. Když nasadíte virtuální síťové zařízení v provozním prostředí, zařízení obvykle filtry, protokoly nebo provádí některé další funkce před předáním provoz. V tomto článku však operační systém jednoduše předává veškerý provoz, které obdrží. Povolení předávání IP v operačním systému virtuálního počítače s [nastavení rozšíření virtuálního az](/cli/azure/vm/extension#az_vm_extension_set), který spouští příkaz, který umožňuje předávání IP v operačním systému.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
Příkaz může trvat až několik minut provést.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte dva virtuální počítače ve virtuální síti, abyste mohli ověřit, že provoz z *veřejné* podsítě se směruje na *privátní* podsítě virtuální zařízení sítě později. 

Vytvoření virtuálního počítače v *veřejné* podsíť s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create). `--no-wait` Parametr povoluje Azure a spusťte příkaz na pozadí, abyste mohli pokračovat na další příkaz. K zjednodušení tohoto článku, je použít heslo. Klíče jsou obvykle používány v nasazení v produkčním prostředí. Pokud chcete použít klíče, je nutné také nakonfigurovat přesměrování agenta SSH. Další informace naleznete v dokumentaci pro vašeho klienta SSH. Nahraďte `<replace-with-your-password>` v následujícím příkazu heslem dle vlastního výběru.

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

Virtuální počítač trvá několik minut pro vytvoření. Po vytvoření virtuálního počítače Azure CLI uvádí informace podobně jako v následujícím příkladu: 

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
Poznamenejte si **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu do pozdějšího kroku.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Směrovat provoz prostřednictvím sítě virtuálního zařízení

Použijte následující příkaz pro vytvoření relace SSH s *myVmMgmt* virtuálního počítače. Nahraďte  *<publicIpAddress>*  s veřejnou IP adresu virtuálního počítače. V příkladu nahoře, IP adresa je *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Po zobrazení výzvy k zadání hesla, zadejte heslo, které jste vybrali v [vytváření virtuálních počítačů](#create-virtual-machines).

Použijte následující příkaz k instalaci traceroute na *myVmMgmt* virtuálního počítače:

```bash 
sudo apt-get install traceroute
```

Použijte následující příkaz k testování směrování pro síťový provoz *myVmWeb* virtuální počítač z *myVmMgmt* virtuálního počítače.

```bash
traceroute myvmweb
```

Odpověď je stejný jako v následujícím příkladu:

```bash
traceroute to myvmweb (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Uvidíte, že provoz se směruje přímo z *myVmMgmt* virtuální počítač *myVmWeb* virtuálního počítače. Azure výchozích tras, směrovat provoz přímo mezi podsítěmi. 

Pomocí následujícího příkazu SSH k *myVmWeb* virtuální počítač z *myVmMgmt* virtuálního počítače:

```bash 
ssh azureuser@myVmWeb
```

Použijte následující příkaz k instalaci traceroute na *myVmWeb* virtuálního počítače:

```bash 
sudo apt-get install traceroute
```

Použijte následující příkaz k testování směrování pro síťový provoz *myVmMgmt* virtuální počítač z *myVmWeb* virtuálního počítače.

```bash
traceroute myvmmgmt
```

Odpověď je stejný jako v následujícím příkladu:

```bash
traceroute to myvmmgmt (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Uvidíte, že prvním skoku je 10.0.2.4, což je virtuální zařízení sítě privátní IP adresu. Druhé směrování je 10.0.1.4, privátní IP adresu *myVmMgmt* virtuálního počítače. Trasy přidat do *myRouteTablePublic* směrovací tabulky a přidružené k *veřejné* podsíť způsobila Azure pro směrování provozu prostřednictvím hodnocení chyb zabezpečení, nikoli přímo do *privátní* podsítě.

Zavřete relace SSH do obou *myVmWeb* a *myVmMgmt* virtuálních počítačů.

## <a name="troubleshoot-routing"></a>Řešení potíží s směrování

Jste se naučili v předchozích krocích, Azure, použije výchozí trasy, lze, Volitelně můžete přepsat pomocí vlastní trasy. V některých případech nemusí být provoz směruje podle očekávání jako. Použití [az sítě sledovacích procesů zobrazit next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) k určení, jak se provoz se směruje mezi dvěma virtuálními počítači. Například následující příkaz testy směrování provozu z *myVmWeb* (10.0.0.4) virtuálního počítače *myVmMgmt* (10.0.1.4) virtuálního počítače:

```azurecli-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region.
az network watcher configure --locations eastus --resource-group myResourceGroup --enabled true

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 10.0.1.4 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVmWeb \
  --out table
```
Tento výstup se vrátí po krátké čekání:

```azurecli
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

Výstup informuje, že dalšího směrování IP adresu pro provoz z *myVmWeb* k *myVmMgmt* je 10.0.2.4 ( *myVmNva* virtuální počítač), typ dalšího směrování je  *VirtualAppliance*, a že je směrovací tabulce, která způsobí, že směrování *myRouteTablePublic*.

Efektivní trasy pro každé síťové rozhraní jsou kombinaci Azure výchozí trasy a všech tras, které definujete. Zobrazit všechny trasy efektivní pro rozhraní sítě ve virtuálním počítači s [az sítě seskupování zobrazit platné trasy tabulky](/cli/azure/network/nic#az_network_nic_show_effective_route_table). Chcete-li například zobrazit efektivní trasy pro *MyVmWebVMNic* síťové rozhraní na *myVmWeb* virtuální počítač, zadejte následující příkaz:

```azurecli-interactive
az network nic show-effective-route-table \
  --name MyVmWebVMNic \
  --resource-group myResourceGroup
```

Jsou vráceny všechny výchozí trasy a trasy, k níž jste přidali v předchozím kroku.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, použijte [odstranění skupiny az](/cli/azure/group#az_group_delete) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

V tomto článku vytvořit směrovací tabulku a přidružené k podsíti. Vytvořili jste virtuální zařízení sítě směrovat přenosy z veřejných podsítě do privátní podsítě. Když nasadíte mnoho prostředků Azure v rámci virtuální sítě, zdrojů u některých služeb Azure PaaS nelze nasadit do virtuální sítě. Můžete dál omezit přístup k prostředkům některé služby Azure PaaS provoz jenom z podsítě virtuální sítě, když. Přechodu na v dalším kurzu se dozvíte, jak omezit přístup k síti na Azure PaaS prostředky.

> [!div class="nextstepaction"]
> [Omezit přístup k síti na PaaS prostředky](virtual-network-service-endpoints-configure.md#azure-cli)
