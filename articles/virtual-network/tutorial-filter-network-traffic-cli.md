---
title: Filtrování provozu sítě přenosů - rozhraní příkazového řádku Azure | Microsoft Docs
description: V tomto článku zjistěte, jak k filtrování provozu sítě pro podsíť s skupinu zabezpečení sítě pomocí rozhraní příkazového řádku Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 11dc0e5f6ee398b2a745ed60cbc166e2a1697c3e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrování provozu sítě s skupinu zabezpečení sítě pomocí rozhraní příkazového řádku Azure

Síťový provoz na příchozí a odchozí z podsítě virtuální sítě můžete filtrovat pomocí skupiny zabezpečení sítě. Skupiny zabezpečení sítě obsahují pravidla zabezpečení, které umožňují filtrovat provoz sítě podle IP adresy, portu a protokolu. Pro prostředky nasazené v podsíti, platí pravidla zabezpečení. V tomto článku získáte informace o těchto tématech:

* Vytvořit skupinu a zabezpečení pravidla zabezpečení sítě
* Vytvoření virtuální sítě a přidružte skupinu zabezpečení sítě pro podsíť
* Nasazení virtuálních počítačů (VM) do podsítě
* Filtry přenosů testu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, v tomto článku vyžaduje, že používáte Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Skupina zabezpečení sítě obsahuje pravidla zabezpečení. Pravidla zabezpečení zadejte zdroj a cíl. Zdroje a cíle může být skupin zabezpečení aplikací.

### <a name="create-application-security-groups"></a>Vytvoření skupin zabezpečení aplikací

Nejprve vytvořte skupinu prostředků pro všechny prostředky, které jsou vytvořené v tomto článku se [vytvořit skupinu az](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků v *eastus* umístění: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Vytvoření skupiny zabezpečení aplikace s [vytvořit az sítě asg](/cli/azure/network/asg#az_network_asg_create). Skupina zabezpečení aplikace umožňuje skupiny serverů s podobnou port filtrování požadavků. Následující příklad vytvoří dvě skupiny zabezpečení aplikace.

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořit skupinu zabezpečení sítě s [vytvořit az sítě nsg](/cli/azure/network/nsg#az_network_nsg_create). Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Vytvoření pravidla zabezpečení

Vytvořit pravidlo zabezpečení s [vytvořit pravidla nsg sítě az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Následující příklad vytvoří pravidlo, které umožňuje přenos příchozí z Internetu, aby *myWebServers* skupiny zabezpečení aplikací přes porty 80 a 443:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

Následující příklad vytvoří pravidlo, které umožňuje přenos příchozí z Internetu, aby *myMgmtServers* skupiny zabezpečení aplikací přes port 22:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

V tomto článku je SSH (port 22) přístup k Internetu pro *myAsgMgmtServers* virtuálních počítačů. Pro provozní prostředí, místo vystavení port 22 k Internetu, se doporučuje, že se připojit k prostředky Azure, které chcete spravovat pomocí [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [privátní](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) síťové připojení.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Následující příklad vytvoří virtuální s názvem *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Přidat podsíť virtuální sítě s [az sítě vnet podsíť vytváření](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Následující příklad přidá podsíť s názvem *mySubnet* do virtuální sítě a známými *myNsg* skupinu zabezpečení sítě k němu:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte dva virtuální počítače ve virtuální síti, abyste mohli ověřit filtrování v pozdější fázi provozu. 

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač, který bude sloužit jako webový server. `--asgs myAsgWebServers` Možnost způsobí, že Azure, aby se vytvoří pro virtuální počítač členem síťové rozhraní *myAsgWebServers* skupiny zabezpečení aplikací.

`--nsg ""` Je zadána možnost zabránit ve vytváření výchozí skupina zabezpečení sítě pro síťové rozhraní, Azure vytvoří při vytváření virtuálního počítače Azure. K zjednodušení tohoto článku, je použít heslo. Klíče jsou obvykle používány v nasazení v produkčním prostředí. Pokud chcete použít klíče, je nutné také nakonfigurovat přesměrování agenta SSH pro zbývající kroky. Další informace naleznete v dokumentaci pro vašeho klienta SSH. Nahraďte `<replace-with-your-password>` v následujícím příkazu heslem dle vlastního výběru.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Virtuální počítač trvá několik minut pro vytvoření. Po vytvoření virtuálního počítače se vrátí výstup podobný v následujícím příkladu: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Poznamenejte si **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu do pozdějšího kroku.  Vytvoření virtuálního počítače sloužit jako server pro správu:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Virtuální počítač trvá několik minut pro vytvoření. Poznámka: Po vytvoření virtuálního počítače **publicIpAddress** ve vrácené výstupu. Tato adresa se používá pro přístup k virtuálnímu počítači v dalším kroku. Nemusíte pokračovat dalším krokem, dokud Azure dokončí vytváření virtuálního počítače.

## <a name="test-traffic-filters"></a>Filtry přenosů testu

Použijte příkaz, který následuje vytvořit relace SSH s *myVmMgmt* virtuálních počítačů. Nahraďte *<publicIpAddress>* s veřejnou IP adresu vašeho virtuálního počítače. V příkladu nahoře, IP adresa je *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Po zobrazení výzvy k zadání hesla, zadejte heslo zadané v [vytvořit virtuální počítače](#create-virtual-machines).

Je připojení úspěšné, protože port 22 je povolena příchozí z Internetu, aby *myAsgMgmtServers* skupina zabezpečení aplikací, která síťové rozhraní připojené k *myVmMgmt* virtuální počítač.

Pomocí následujícího příkazu SSH k *myVmWeb* virtuální počítač z *myVmMgmt* virtuálních počítačů:

```bash 
ssh azureuser@myVmWeb
```

Je připojení úspěšné, protože výchozí pravidlo zabezpečení v rámci jednotlivých skupin zabezpečení sítě umožňuje provoz přes všechny porty mezi všechny IP adresy v rámci virtuální sítě. Nelze SSH k *myVmWeb* virtuálních počítačů z Internetu protože pravidla zabezpečení pro *myAsgWebServers* neumožňuje port 22 příchozí z Internetu.

Použijte následující příkazy pro instalaci webového serveru nginx na *myVmWeb* virtuálních počítačů:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

*MyVmWeb* virtuální počítač může odchozí do Internetu pro načítání nginx, protože výchozí pravidlo zabezpečení umožňuje, aby všechny odchozí přenosy na Internetu. Ukončení *myVmWeb* relace SSH, což ponechá na `username@myVmMgmt:~$` výzvy z *myVmMgmt* virtuálních počítačů. Načíst na úvodní obrazovce nginx z *myVmWeb* virtuální počítač, zadejte následující příkaz:

```bash
curl myVmWeb
```

Odhlaste se z *myVmMgmt* virtuálních počítačů. Potvrďte, že máte přístup *myVmWeb* webový server z mimo Azure, zadejte `curl <publicIpAddress>` ze svého počítače. Je připojení úspěšné, protože port 80 je povolena příchozí z Internetu, aby *myAsgWebServers* skupina zabezpečení aplikací, která síťové rozhraní připojené k *myVmWeb* virtuální počítač.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, použijte [odstranění skupiny az](/cli/azure/group#az_group_delete) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili skupinu zabezpečení sítě a přidružené k podsíti virtuální sítě. Další informace o skupinách zabezpečení sítě najdete v tématu [přehled skupiny zabezpečení sítě](security-overview.md) a [spravovat skupinu zabezpečení sítě](manage-network-security-group.md).

Provoz Azure směrování mezi podsítěmi ve výchozím nastavení. Místo toho můžete směrovat provoz mezi podsítěmi pomocí virtuálních počítačů, slouží jako bránu firewall, například. Další informace, jak zjistit, [vytvořit směrovací tabulku](tutorial-create-route-table-cli.md).