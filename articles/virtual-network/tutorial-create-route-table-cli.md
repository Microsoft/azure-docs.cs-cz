---
title: Směrování síťového provozu – rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak ke směrování síťového provozu s směrovací tabulky pomocí rozhraní příkazového řádku Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 89ac87436fb6807177acf3882dd6e923b1722bd5
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54849205"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Směrování provozu sítě s směrovací tabulky pomocí rozhraní příkazového řádku Azure

Azure ve výchozím nastavení automaticky směruje provoz mezi všemi podsítěmi v rámci virtuální sítě. Můžete vytvořit vlastní trasy a přepsat tak výchozí směrování Azure. Možnost vytvářet vlastní trasy je užitečná například v případě, že chcete směrovat provoz mezi podsítěmi přes síťové virtuální zařízení. V tomto článku získáte informace o těchto tématech:

* Vytvoření směrovací tabulky
* Vytvoření trasy
* Vytvoření virtuální sítě s několika podsítěmi
* Přidružení směrovací tabulky k podsíti
* Vytvoření síťového virtuálního zařízení, které směruje provoz
* Nasazení virtuálních počítačů do různých podsítí
* Směrování provozu z jedné podsítě do jiné přes síťové virtuální zařízení

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

Než vytvoříte směrovací tabulku, vytvořte skupinu prostředků s [vytvořit skupiny az](/cli/azure/group#az_group_create) pro všechny prostředky vytvořené v tomto článku. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

Vytvoření směrovací tabulky s [vytvořit sítě az route-table](/cli/azure/network/route-table#az-network-route-table-create). Následující příklad vytvoří směrovací tabulku s názvem *myRouteTablePublic*. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Vytvoření trasy

Vytvoření trasy ve směrovací tabulce s [az network route-table postupu vytvoření](/cli/azure/network/route-table/route#az-network-route-table-route-create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

Než budete moct přidružit směrovací tabulky k podsíti, budete muset vytvořit virtuální síť a podsíť. Vytvoření virtuální sítě s jednou podsítí s [az network vnet vytvořit](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Vytvořte dva další podsítě s [az podsíti virtuální sítě vytvořit](/cli/azure/network/vnet/subnet).

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

Přidružit *myRouteTablePublic* tabulky tras, abyste *veřejné* podsíť s [az network vnet podsíť aktualizace](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Vytvoření síťového virtuálního zařízení

Síťové virtuální zařízení je virtuální počítač, který provádí síťovou funkci, jako je směrování, brána firewall nebo optimalizace sítě WAN.

Vytvoření síťového virtuálního zařízení v *DMZ* podsíť s [az vm vytvořit](/cli/azure/vm#az_vm_create). Při vytváření virtuálního počítače Azure vytvoří a přiřadí veřejnou IP adresu virtuálního počítače, ve výchozím nastavení. `--public-ip-address ""` Parametr instruuje Azure vytvořit a přiřadit veřejnou IP adresu virtuálního počítače, protože virtuální počítač nemusí být připojen k z Internetu. Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

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

Vytvoření virtuálního počítače trvá několik minut. Nepokračujte k dalšímu kroku dokud Azure nedokončí vytváření virtuálního počítače a vrátí výstup o virtuálním počítači. 

Aby síťové rozhraní mohlo směrovat síťový provoz, který se do něj odešle a který není určený pro jeho vlastní IP adresu, musí být pro síťové rozhraní povolené předávání IP. Povolit předávání IP pro síťové rozhraní s [az network nic update](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

Operační systém nebo aplikace spuštěná v rámci virtuálního počítače musí také být schopné směrovat síťový provoz. Povolení předávání IP v rámci Virtuálního počítače s operačním systémem [sada rozšíření az vm](/cli/azure/vm/extension#az_vm_extension_set):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
Příkaz může trvat až jednu minutu ke spuštění.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvoření dvou virtuálních počítačů ve virtuální síti, abyste mohli ověřit provozu z *veřejné* podsítě se směruje na *privátní* podsítě v pozdějším kroku. 

Vytvářet virtuální počítače *veřejné* podsíť s [az vm vytvořit](/cli/azure/vm#az_vm_create). `--no-wait` Parametr umožňuje systému Azure, aby příkaz spustila na pozadí, takže můžete pokračovat k dalšímu příkazu. Pro zjednodušení tohoto článku, je použít heslo. Klíče se obvykle používají v produkčním prostředí. Pokud používáte klíče, musíte také nakonfigurovat přesměrování agenta SSH. Další informace naleznete v dokumentaci k vašemu klientovi SSH. Nahraďte `<replace-with-your-password>` v následujícím příkazu s heslem podle vašeho výběru.

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

Vytvářet virtuální počítače *privátní* podsítě.

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

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření virtuálního počítače Azure CLI zobrazí podobné informace jako v následujícím příkladu: 

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
Poznamenejte si hodnotu **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu v pozdějším kroku.

## <a name="route-traffic-through-an-nva"></a>Směrování provozu přes síťové virtuální zařízení

Pomocí následujícího příkazu vytvořte relaci SSH s *myVmPrivate* virtuálního počítače. Nahraďte *<publicIpAddress>* veřejnou IP adresou vašeho virtuálního počítače. V předchozím příkladu je IP adresa *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Po zobrazení výzvy k zadání hesla, zadejte heslo, které jste vybrali v [vytvářet virtuální počítače](#create-virtual-machines).

Použijte následující příkaz k instalaci na trasování tras *myVmPrivate* virtuálního počítače:

```bash 
sudo apt-get install traceroute
```

Použijte následující příkaz k otestování směrování pro síťový provoz *myVmPublic* z virtuálního počítače *myVmPrivate* virtuálního počítače.

```bash
traceroute myVmPublic
```

Odpověď bude podobná jako v následujícím příkladu:

```bash
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Jak vidíte, provoz se směruje přímo z virtuálního počítače *myVmPrivate* do virtuálního počítače *myVmPublic*. Výchozí trasy, směrovat provoz přímo mezi podsítěmi. 

Použijte následující příkaz pro připojení SSH k *myVmPublic* z virtuálního počítače *myVmPrivate* virtuálního počítače:

```bash 
ssh azureuser@myVmPublic
```

Použijte následující příkaz k instalaci na trasování tras *myVmPublic* virtuálního počítače:

```bash 
sudo apt-get install traceroute
```

Použijte následující příkaz k otestování směrování pro síťový provoz *myVmPrivate* z virtuálního počítače *myVmPublic* virtuálního počítače.

```bash
traceroute myVmPrivate
```

Odpověď bude podobná jako v následujícím příkladu:

```bash
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Jak vidíte, první segment směrování je 10.0.2.4, což je privátní IP adresa síťového virtuálního zařízení. Druhý segment směrování je 10.0.1.4, což je privátní IP adresa virtuálního počítače *myVmPrivate*. Trasa přidaná do směrovací tabulky *myRouteTablePublic* a přidružená k podsíti *Public* způsobila, že Azure směruje provoz přes síťové virtuální zařízení, a ne přímo do podsítě *Private*.

Zavření relace SSH k oběma *myVmPublic* a *myVmPrivate* virtuálních počítačů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, použijte [odstranění skupiny az](/cli/azure/group#az_group_delete) k odebrání skupiny prostředků a všech prostředků, které obsahuje.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili směrovací tabulku a přidruženou k podsíti. Vytvořili jste jednoduché síťové virtuální zařízení, které směrovalo provoz z veřejné podsítě do privátní podsítě. Z webu [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) můžete nasadit různá předem nakonfigurovaná síťová virtuální zařízení, která provádí síťové funkce, jako je brána firewall a optimalizace sítě WAN. Další informace o směrování najdete v tématech [Přehled směrování](virtual-networks-udr-overview.md) a [Správa směrovací tabulky](manage-route-table.md).

Přestože v rámci virtuální sítě můžete nasadit řadu prostředků Azure, prostředky některých služeb Azure PaaS do virtuální sítě nasadit nejde. Přesto můžete omezit přístup k prostředkům některých služeb Azure PaaS pouze pro provoz z podsítě virtuální sítě. Další informace o postupu [omezení síťového přístupu k prostředkům PaaS](tutorial-restrict-network-access-to-resources-cli.md).
