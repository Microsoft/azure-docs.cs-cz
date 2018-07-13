---
title: Filtrování síťového provozu – rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak filtrovat síťový provoz do podsítě se skupinou zabezpečení sítě pomocí Azure CLI.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38721063"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrování síťového provozu se skupinou zabezpečení sítě pomocí Azure CLI

Příchozí a odchozí provoz podsítě virtuální sítě můžete filtrovat pomocí skupiny zabezpečení sítě. Skupiny zabezpečení sítě obsahují pravidla zabezpečení, která filtrují síťový provoz podle IP adresy, portu a protokolu. Pravidla zabezpečení se vztahují na prostředky nasazené v podsíti. V tomto článku získáte informace o těchto tématech:

* Vytvoření skupiny zabezpečení sítě a pravidel zabezpečení
* Vytvoření virtuální sítě a přidružení skupiny zabezpečení sítě k podsíti
* Nasazení virtuálních počítačů do podsítě
* Testování filtrů provozu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, tento článek vyžaduje použití Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Skupina zabezpečení sítě obsahuje pravidla zabezpečení. Pravidla zabezpečení určují zdroj a cíl. Zdroji a cíli můžou být skupiny zabezpečení aplikací.

### <a name="create-application-security-groups"></a>Vytvoření skupin zabezpečení aplikací

Nejprve vytvořte skupinu prostředků pro všechny prostředky vytvořené v tomto článku se [vytvořit skupiny az](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků v umístění *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Vytvoření skupiny zabezpečení aplikací s [az sítě asg vytvořit](/cli/azure/network/asg#az_network_asg_create). Skupina zabezpečení aplikací umožňuje seskupovat servery s podobnými požadavky na filtrování portů. Následující příklad vytvoří dvě skupiny zabezpečení aplikací.

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

Vytvořte skupinu zabezpečení sítě pomocí [az network nsg vytvořit](/cli/azure/network/nsg#az_network_nsg_create). Následující příklad vytvoří skupinu zabezpečení sítě *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Vytvoření pravidel zabezpečení

Vytvořit pravidlo zabezpečení s [az network nsg pravidlo vytvořte](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Následující příklad vytvoří pravidlo povolující příchozí provoz z internetu do skupiny zabezpečení aplikací *myWebServers* na portech 80 a 443:

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

Následující příklad vytvoří pravidlo, které umožní provoz z Internetu do příchozí *myMgmtServers* skupiny zabezpečení aplikací přes port 22:

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

V tomto článku je SSH (port 22) přístupný na Internetu *myAsgMgmtServers* virtuálního počítače. Pro produkční prostředí, místo zveřejňování portu 22 na Internetu, je doporučujeme připojit se k prostředkům Azure, které chcete spravovat pomocí [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [privátní](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) síťové připojení.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Následující příklad vytvoří virtuální síť *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Přidání podsítě do virtuální sítě pomocí [az podsíti virtuální sítě vytvořit](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Následující příklad do virtuální sítě přidá podsíť *mySubnet* a přidruží k ní skupinu zabezpečení sítě *myNsg*:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače, abyste v pozdějším kroku mohli ověřit filtrování provozu. 

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač, který bude sloužit jako webový server. `--asgs myAsgWebServers` Možnost způsobí, že Azure vytváří vytvoří pro virtuální počítač členem síťové rozhraní *myAsgWebServers* skupinu zabezpečení aplikace.

`--nsg ""` Je zadána možnost, chcete-li zabránit ve vytváření výchozí skupiny zabezpečení sítě pro síťové rozhraní Azure vytvoří při vytváření virtuálního počítače Azure. Pro zjednodušení tohoto článku, je použít heslo. Klíče se obvykle používají v produkčním prostředí. Pokud používáte klíče, musíte také nakonfigurovat přesměrování agenta SSH pro zbývající kroky. Další informace naleznete v dokumentaci k vašemu klientovi SSH. Nahraďte `<replace-with-your-password>` v následujícím příkazu s heslem podle vašeho výběru.

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

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření virtuálního počítače se nevrátí výstup podobný následujícímu příkladu: 

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

Poznamenejte si hodnotu **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu v pozdějším kroku.  Vytvořte virtuální počítač, který bude sloužit jako server pro správu:

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

Vytvoření virtuálního počítače trvá několik minut. Poznámka: Po vytvoření virtuálního počítače **publicIpAddress** ve vrácené výstupu. Tato adresa se používá pro přístup k virtuálnímu počítači v dalším kroku. Nepokračujte dalším krokem, dokud Azure nedokončí vytváření virtuálního počítače.

## <a name="test-traffic-filters"></a>Testování filtrů provozu

Použijte k vytvoření relace SSH pomocí následujícího příkazu *myVmMgmt* virtuálního počítače. Nahraďte *<publicIpAddress>* veřejnou IP adresou vašeho virtuálního počítače. V předchozím příkladu je IP adresa *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Po zobrazení výzvy k zadání hesla, zadejte heslo, které jste zadali v [vytvoření virtuálních počítačů](#create-virtual-machines).

Připojení bude úspěšné, protože je povolený příchozí provoz z Internetu do portu 22 *myAsgMgmtServers* skupiny zabezpečení aplikací, který obsahuje síťové rozhraní připojené k *myVmMgmt* virtuální počítač.

Použijte následující příkaz pro připojení SSH k *myVmWeb* z virtuálního počítače *myVmMgmt* virtuálního počítače:

```bash 
ssh azureuser@myVmWeb
```

Připojení bude úspěšné, protože výchozí pravidlo zabezpečení v obou skupinách zabezpečení sítě povoluje provoz na všech portech mezi všemi IP adresami v rámci virtuální sítě. Není možné SSH *myVmWeb* virtuálnímu počítači z Internetu protože pravidlo zabezpečení pro *myAsgWebServers* neumožňuje port 22 příchozí provoz z Internetu.

Pomocí následujících příkazů nainstalujte webový server nginx na *myVmWeb* virtuálního počítače:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

*MyVmWeb* virtuálního počítače je povolený jako odchozí k Internetu a načíst nginx, protože výchozí pravidlo zabezpečení umožňuje veškerý odchozí provoz do Internetu. Ukončení *myVmWeb* relaci SSH, zůstanete na `username@myVmMgmt:~$` příkazový řádek z *myVmMgmt* virtuálního počítače. K načtení na úvodní obrazovce serveru nginx z *myVmWeb* virtuálního počítače, zadejte následující příkaz:

```bash
curl myVmWeb
```

Odhlaste se z *myVmMgmt* virtuálního počítače. Potvrďte, zda máte přístup *myVmWeb* webovému serveru mimo Azure, zadejte `curl <publicIpAddress>` ze svého počítače. Připojení bude úspěšné, protože je povolený příchozí provoz z Internetu do portu 80 *myAsgWebServers* skupiny zabezpečení aplikací, který obsahuje síťové rozhraní připojené k *myVmWeb* virtuální počítač je v.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, použijte [odstranění skupiny az](/cli/azure/group#az_group_delete) k odebrání skupiny prostředků a všech prostředků, které obsahuje.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili skupinu zabezpečení sítě a přidružené k podsíti virtuální sítě. Další informace o skupinách zabezpečení sítě najdete v tématech [Přehled skupin zabezpečení sítě](security-overview.md) a [Správa skupiny zabezpečení sítě](manage-network-security-group.md).

Provoz mezi podsítěmi směruje ve výchozím nastavení Azure. Místo toho se můžete rozhodnout směrovat provoz mezi podsítěmi například prostřednictvím virtuálního počítače, který slouží jako brána firewall. Další informace o postupu [vytvořit směrovací tabulku](tutorial-create-route-table-cli.md).