---
title: Filtrování síťového provozu – Azure CLI | Microsoft Docs
description: V tomto článku se dozvíte, jak filtrovat síťový provoz do podsítě a pomocí skupiny zabezpečení sítě pomocí Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7589b13dc517a23e8d9d65907fb3342e4e2490a9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000585"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrování síťového provozu pomocí skupiny zabezpečení sítě pomocí Azure CLI

Příchozí a odchozí provoz podsítě virtuální sítě můžete filtrovat pomocí skupiny zabezpečení sítě. Skupiny zabezpečení sítě obsahují pravidla zabezpečení, která filtrují síťový provoz podle IP adresy, portu a protokolu. Pravidla zabezpečení se vztahují na prostředky nasazené v podsíti. V tomto článku získáte informace o těchto tématech:

* Vytvoření skupiny zabezpečení sítě a pravidel zabezpečení
* Vytvoření virtuální sítě a přidružení skupiny zabezpečení sítě k podsíti
* Nasazení virtuálních počítačů do podsítě
* Testování filtrů provozu

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Skupina zabezpečení sítě obsahuje pravidla zabezpečení. Pravidla zabezpečení určují zdroj a cíl. Zdroji a cíli můžou být skupiny zabezpečení aplikací.

### <a name="create-application-security-groups"></a>Vytvoření skupin zabezpečení aplikací

Nejprve vytvořte skupinu prostředků pro všechny prostředky vytvořené v tomto článku pomocí [AZ Group Create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků v umístění *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Vytvořte skupinu zabezpečení aplikace pomocí [AZ Network ASG Create](/cli/azure/network/asg). Skupina zabezpečení aplikací umožňuje seskupovat servery s podobnými požadavky na filtrování portů. Následující příklad vytvoří dvě skupiny zabezpečení aplikací.

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

Vytvořte skupinu zabezpečení sítě pomocí [AZ Network NSG Create](/cli/azure/network/nsg). Následující příklad vytvoří skupinu zabezpečení sítě *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Vytvoření pravidel zabezpečení

Vytvořte pravidlo zabezpečení pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule). Následující příklad vytvoří pravidlo povolující příchozí provoz z internetu do skupiny zabezpečení aplikací *myWebServers* na portech 80 a 443:

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

Následující příklad vytvoří pravidlo, které umožňuje příchozí provoz z Internetu do skupiny zabezpečení aplikací *myMgmtServers* přes port 22:

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

V tomto článku se k Internetu pro virtuální počítač *myAsgMgmtServers* zveřejňuje SSH (port 22). U produkčních prostředí se místo vystavení portu 22 k Internetu doporučuje připojit k prostředkům Azure, které chcete spravovat, pomocí [sítě VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [privátního](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) síťového připojení.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet). Následující příklad vytvoří virtuální síť *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Přidejte podsíť do virtuální sítě pomocí [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet). Následující příklad do virtuální sítě přidá podsíť *mySubnet* a přidruží k ní skupinu zabezpečení sítě *myNsg*:

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

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Následující příklad vytvoří virtuální počítač, který bude sloužit jako webový server. `--asgs myAsgWebServers`Možnost způsobí, že Azure zpřístupní síťové rozhraní, které vytvoří pro virtuální počítač jako člena skupiny zabezpečení aplikací *myAsgWebServers* .

Tato `--nsg ""` možnost je určená k tomu, aby se zabránilo tomu, aby Azure vytvořil výchozí skupinu zabezpečení sítě pro síťové rozhraní, které Azure vytvoří při vytváření virtuálního počítače. Pro zjednodušení tohoto článku se používá heslo. Klíče se obvykle používají v produkčních nasazeních. Pokud používáte klíče, musíte také nakonfigurovat přesměrování agenta SSH pro zbývající kroky. Další informace najdete v dokumentaci ke klientovi SSH. `<replace-with-your-password>`V následujícím příkazu nahraďte heslem, které jste si zvolili.

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

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření virtuálního počítače se vrátí výstup podobný následujícímu příkladu: 

```output
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

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření virtuálního počítače si poznamenejte **publicIpAddress** ve vráceném výstupu. Tato adresa se používá pro přístup k virtuálnímu počítači v dalším kroku. Nepokračujte dalším krokem, dokud Azure nedokončí vytváření virtuálního počítače.

## <a name="test-traffic-filters"></a>Testování filtrů provozu

Pomocí následujícího příkazu vytvořte relaci SSH s virtuálním počítačem s *myVmMgmt* . Nahraďte *\<publicIpAddress>* veřejnou IP adresou vašeho virtuálního počítače. V předchozím příkladu je IP adresa *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Po zobrazení výzvy k zadání hesla zadejte heslo, které jste zadali v části [Vytvoření virtuálních počítačů](#create-virtual-machines).

Připojení je úspěšné, protože port 22 je povolený z Internetu do skupiny zabezpečení aplikace *myAsgMgmtServers* , ve které je síťové rozhraní připojené k virtuálnímu počítači *myVmMgmt* .

Z virtuálního počítače *myVmMgmt* použijte následující příkaz pro SSH k virtuálnímu počítači s *myVmWeb* :

```bash 
ssh azureuser@myVmWeb
```

Připojení bude úspěšné, protože výchozí pravidlo zabezpečení v obou skupinách zabezpečení sítě povoluje provoz na všech portech mezi všemi IP adresami v rámci virtuální sítě. K virtuálnímu počítači s *myVmWeb* se nemůžete pomocí protokolu SSH z Internetu, protože pravidlo zabezpečení pro *myAsgWebServers* nepovoluje port 22 příchozí z Internetu.

K instalaci webového serveru Nginx na virtuálním počítači s *myVmWeb* použijte následující příkazy:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Virtuální počítač *myVmWeb* je povolený pro načtení Nginx z Internetu, protože výchozí pravidlo zabezpečení povoluje veškerý odchozí provoz na Internet. Ukončete relaci SSH *myVmWeb* , která vám ponechá `username@myVmMgmt:~$` výzvu k zadání virtuálního počítače s *myVmMgmt* . Pokud chcete načíst úvodní obrazovku Nginx z virtuálního počítače *myVmWeb* , zadejte následující příkaz:

```bash
curl myVmWeb
```

Odhlaste se k virtuálnímu počítači *myVmMgmt* . Pokud chcete potvrdit, že máte přístup k webovému serveru *myVmWeb* mimo Azure, zadejte `curl <publicIpAddress>` z vlastního počítače. Připojení je úspěšné, protože port 80 je povolený pro příchozí z Internetu do skupiny zabezpečení aplikací *myAsgWebServers* , ve které je síťové rozhraní připojené k virtuálnímu počítači *myVmWeb* .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků a všechny prostředky, které obsahuje, pomocí [AZ Group Delete](/cli/azure/group) .

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili skupinu zabezpečení sítě a přidružíte ji k podsíti virtuální sítě. Další informace o skupinách zabezpečení sítě najdete v tématech [Přehled skupin zabezpečení sítě](security-overview.md) a [Správa skupiny zabezpečení sítě](manage-network-security-group.md).

Provoz mezi podsítěmi směruje ve výchozím nastavení Azure. Místo toho se můžete rozhodnout směrovat provoz mezi podsítěmi například prostřednictvím virtuálního počítače, který slouží jako brána firewall. Další informace o postupu najdete v tématu [Vytvoření směrovací tabulky](tutorial-create-route-table-cli.md).
