---
title: Provoz v síti tras – Azure CLI | Dokumenty společnosti Microsoft
description: V tomto článku se dozvíte, jak směrovat síťový provoz pomocí směrovací tabulky pomocí azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
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
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 5fa94b93e081ab6334c39b848068f50682f5f1f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235052"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Směrování síťového provozu pomocí směrovací tabulky pomocí příkazového příkazového příkazu Azure

Azure ve výchozím nastavení automaticky směruje provoz mezi všemi podsítěmi v rámci virtuální sítě. Můžete vytvořit vlastní trasy a přepsat tak výchozí směrování Azure. Možnost vytvářet vlastní trasy je užitečná například v případě, že chcete směrovat provoz mezi podsítěmi přes síťové virtuální zařízení. V tomto článku získáte informace o těchto tématech:

* Vytvoření směrovací tabulky
* Vytvoření trasy
* Vytvoření virtuální sítě s několika podsítěmi
* Přidružení směrovací tabulky k podsíti
* Vytvoření síťového virtuálního zařízení, které směruje provoz
* Nasazení virtuálních počítačů do různých podsítí
* Směrování provozu z jedné podsítě do jiné přes síťové virtuální zařízení

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

Před vytvořením tabulky tras vytvořte skupinu prostředků s [vytvořením skupiny az](/cli/azure/group) pro všechny zdroje vytvořené v tomto článku. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

Vytvořte směrovací tabulku s [vytvořením az síťové směrovací tabulky](/cli/azure/network/route-table#az-network-route-table-create). Následující příklad vytvoří tabulku tras s názvem *myRouteTablePublic*. 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Vytvoření trasy

Vytvořte trasu v tabulce tras s [vytvořením trasy az sítě route-table](/cli/azure/network/route-table/route#az-network-route-table-route-create). 

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

Před přidružením směrovací tabulky k podsíti je třeba vytvořit virtuální síť a podsíť. Vytvořte virtuální síť s jednou podsítí s [vytvořením virtuální sítě AZ](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Vytvořte dvě další podsítě s [vytvořením podsítě sítě AZ](/cli/azure/network/vnet/subnet).

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

Přidružte tabulku směrování *myRouteTablePublic* k *veřejné* podsíti s [aktualizací podsítě sítě AZ](/cli/azure/network/vnet/subnet).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Vytvoření síťového virtuálního zařízení

Síťové virtuální zařízení je virtuální počítač, který provádí síťovou funkci, jako je směrování, brána firewall nebo optimalizace sítě WAN.

Vytvořte síťové virtuální sítě v podsíti *DMZ* pomocí [vytvoření az vm](/cli/azure/vm). Když vytvoříte virtuální počítač, Azure ve výchozím nastavení vytvoří a přiřadí virtuálnímu počítači veřejnou IP adresu. Parametr `--public-ip-address ""` instruuje Azure, aby nevytvářel a přiřazoval virtuálnímu počítači veřejnou IP adresu, protože virtuální počítač nemusí být připojený z internetu. Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Nepokračujte k dalšímu kroku, dokud Azure nedokončí vytváření virtuálního počítače a vrátí výstup o virtuálním počítači. 

Aby síťové rozhraní mohlo směrovat síťový provoz, který se do něj odešle a který není určený pro jeho vlastní IP adresu, musí být pro síťové rozhraní povolené předávání IP. Povolte předávání IP adres pro síťové rozhraní pomocí [aktualizace az network nic](/cli/azure/network/nic).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

Operační systém nebo aplikace spuštěná v rámci virtuálního počítače musí také být schopné směrovat síťový provoz. Povolit předávání IP adres v rámci operačního systému virtuálního počítačů s [rozšiřující sadou az vm](/cli/azure/vm/extension):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```

Provedení příkazu může trvat až minutu.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte dva virtuální počítače ve virtuální síti, abyste mohli ověřit, že provoz z *veřejné* podsítě je směrován do *soukromé* podsítě prostřednictvím síťového virtuálního zařízení v pozdějším kroku. 

Vytvořte virtuální virtuální ho v *veřejné* podsíti s [vytvořením virtuálního va](/cli/azure/vm). Parametr `--no-wait` umožňuje Azure spustit příkaz na pozadí, takže můžete pokračovat na další příkaz. Chcete-li zefektivnit tento článek, heslo se používá. Klíče se obvykle používají v produkčních nasazeních. Pokud používáte klíče, musíte také nakonfigurovat předávání agentů SSH. Další informace naleznete v dokumentaci pro vašeho klienta SSH. Nahraďte `<replace-with-your-password>` v následujícím příkazu heslo podle vašeho výběru.

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

Vytvořte virtuální hod v *soukromé* podsíti.

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

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření virtuálního počítače azure cli zobrazí informace podobné v následujícím příkladu: 

```output
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

Poznamenejte si hodnotu **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu virtuálnímu mněmu z internetu v pozdějším kroku.

## <a name="route-traffic-through-an-nva"></a>Směrování provozu přes síťové virtuální zařízení

Pomocí následujícího příkazu vytvořte relaci SSH s virtuálním virtuálním mem *myVmPrivate.* Nahraďte * \<>publicIpAddress* veřejnou IP adresou virtuálního počítače. Ve výše uvedeném příkladu je adresa IP *13.90.242.231*.

```bash
ssh azureuser@<publicIpAddress>
```

Po zobrazení výzvy k zadání hesla zadejte heslo, které jste vybrali v části [Vytvořit virtuální počítače](#create-virtual-machines).

Pomocí následujícího příkazu nainstalujte trasovací trasování na virtuální počítač *myVmPrivate:*

```bash
sudo apt-get install traceroute
```

Pomocí následujícího příkazu otestujte směrování pro síťový provoz na virtuální ms *myVmPublic* z virtuálního virtuálního mm *myVmPrivate.*

```bash
traceroute myVmPublic
```

Odpověď bude podobná jako v následujícím příkladu:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Jak vidíte, provoz se směruje přímo z virtuálního počítače *myVmPrivate* do virtuálního počítače *myVmPublic*. Výchozí trasy Azure, směrovat provoz přímo mezi podsítěmi. 

Použijte následující příkaz pro SSH na *myVmPublic* VM z virtuálního virtuálního měna *myVmPrivate:*

```bash
ssh azureuser@myVmPublic
```

Pomocí následujícího příkazu nainstalujte trasovací trasování na virtuální počítač *myVmPublic:*

```bash
sudo apt-get install traceroute
```

Pomocí následujícího příkazu otestujte směrování pro síťový provoz na virtuální ms *myVmPrivate* z virtuálního virtuálního mm *myVmPublic.*

```bash
traceroute myVmPrivate
```

Odpověď bude podobná jako v následujícím příkladu:

```output
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Jak vidíte, první segment směrování je 10.0.2.4, což je privátní IP adresa síťového virtuálního zařízení. Druhý segment směrování je 10.0.1.4, což je privátní IP adresa virtuálního počítače *myVmPrivate*. Trasa přidaná do směrovací tabulky *myRouteTablePublic* a přidružená k podsíti *Public* způsobila, že Azure směruje provoz přes síťové virtuální zařízení, a ne přímo do podsítě *Private*.

Zavřete relace SSH pro virtuální chod *myVmPublic* i *myVmPrivate.*

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, použijte [az skupiny odstranit](/cli/azure/group) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili tabulku tras a přidružili ji k podsíti. Vytvořili jste jednoduché síťové virtuální zařízení, které směrovalo provoz z veřejné podsítě do privátní podsítě. Z webu [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) můžete nasadit různá předem nakonfigurovaná síťová virtuální zařízení, která provádí síťové funkce, jako je brána firewall a optimalizace sítě WAN. Další informace o směrování najdete v tématech [Přehled směrování](virtual-networks-udr-overview.md) a [Správa směrovací tabulky](manage-route-table.md).

Přestože v rámci virtuální sítě můžete nasadit řadu prostředků Azure, prostředky některých služeb Azure PaaS do virtuální sítě nasadit nejde. Přesto můžete omezit přístup k prostředkům některých služeb Azure PaaS pouze pro provoz z podsítě virtuální sítě. Informace o tom, jak, naleznete [v tématu Omezení přístupu k síti k prostředkům PaaS](tutorial-restrict-network-access-to-resources-cli.md).
