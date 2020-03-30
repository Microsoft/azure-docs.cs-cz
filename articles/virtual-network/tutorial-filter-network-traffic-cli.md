---
title: Filtrování síťového provozu – Azure CLI | Dokumenty společnosti Microsoft
description: V tomto článku se dozvíte, jak filtrovat síťový provoz do podsítě se skupinou zabezpečení sítě pomocí rozhraní příkazového příkazového příkazu k síti Azure.
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
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 72c8b4d57b5064af34665cff1386179e62324938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235082"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrování síťových přenosů se skupinou zabezpečení sítě pomocí příkazového příkazového příkazu Azure

Příchozí a odchozí provoz podsítě virtuální sítě můžete filtrovat pomocí skupiny zabezpečení sítě. Skupiny zabezpečení sítě obsahují pravidla zabezpečení, která filtrují síťový provoz podle IP adresy, portu a protokolu. Pravidla zabezpečení se vztahují na prostředky nasazené v podsíti. V tomto článku získáte informace o těchto tématech:

* Vytvoření skupiny zabezpečení sítě a pravidel zabezpečení
* Vytvoření virtuální sítě a přidružení skupiny zabezpečení sítě k podsíti
* Nasazení virtuálních počítačů do podsítě
* Testování filtrů provozu

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, tento článek vyžaduje, abyste spouštěli Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Skupina zabezpečení sítě obsahuje pravidla zabezpečení. Pravidla zabezpečení určují zdroj a cíl. Zdroji a cíli můžou být skupiny zabezpečení aplikací.

### <a name="create-application-security-groups"></a>Vytvoření skupin zabezpečení aplikací

Nejprve vytvořte skupinu prostředků pro všechny prostředky vytvořené v tomto článku pomocí [vytvoření skupiny AZ](/cli/azure/group). Následující příklad vytvoří skupinu prostředků v umístění *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Vytvořte skupinu zabezpečení aplikace pomocí [az network asg create](/cli/azure/network/asg). Skupina zabezpečení aplikací umožňuje seskupovat servery s podobnými požadavky na filtrování portů. Následující příklad vytvoří dvě skupiny zabezpečení aplikací.

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

Vytvořte skupinu zabezpečení sítě pomocí vytvoření [sítě az nsg](/cli/azure/network/nsg). Následující příklad vytvoří skupinu zabezpečení sítě *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Vytvoření pravidel zabezpečení

Vytvořte pravidlo zabezpečení pomocí [vytvoření pravidla az network nsg](/cli/azure/network/nsg/rule). Následující příklad vytvoří pravidlo povolující příchozí provoz z internetu do skupiny zabezpečení aplikací *myWebServers* na portech 80 a 443:

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

Následující příklad vytvoří pravidlo, které umožňuje přenosy příchozí z Internetu do skupiny zabezpečení aplikace *myMgmtServers* přes port 22:

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

V tomto článku SSH (port 22) je vystavena internetu pro *myAsgMgmtServers* VM. Pro produkční prostředí se namísto vystavení portu 22 internetu doporučujete připojit k prostředkům Azure, které chcete spravovat pomocí připojení [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [privátní](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sítě.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet). Následující příklad vytvoří virtuální síť *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Přidání podsítě do virtuální sítě s [vytvořením podsítě sítě AZ](/cli/azure/network/vnet/subnet). Následující příklad do virtuální sítě přidá podsíť *mySubnet* a přidruží k ní skupinu zabezpečení sítě *myNsg*:

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

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Následující příklad vytvoří virtuální počítač, který bude sloužit jako webový server. Tato `--asgs myAsgWebServers` možnost způsobí, že Azure vytvoří síťové rozhraní, které vytvoří pro virtuální počítač, jako člena skupiny zabezpečení aplikace *myAsgWebServers.*

Možnost `--nsg ""` je určena, aby se zabránilo Azure ve vytvoření výchozí skupiny zabezpečení sítě pro síťové rozhraní, které Azure vytvoří při vytváření virtuálního počítače. Chcete-li zefektivnit tento článek, heslo se používá. Klíče se obvykle používají v produkčních nasazeních. Pokud používáte klíče, musíte také nakonfigurovat předávání agentů SSH pro zbývající kroky. Další informace naleznete v dokumentaci pro vašeho klienta SSH. Nahraďte `<replace-with-your-password>` v následujícím příkazu heslo podle vašeho výběru.

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

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření virtuálního virtuálního zařízení je vrácen výstup podobný následujícímu příkladu: 

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

Poznamenejte si hodnotu **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu virtuálnímu mněmu z internetu v pozdějším kroku.  Vytvořte virtuální počítač, který bude sloužit jako server pro správu:

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

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření virtuálního počítačů si poznamenejte **publicIpAddress** ve vráceném výstupu. Tato adresa se používá pro přístup k virtuálnímu virtuálnímu serveru v dalším kroku. Nepokračujte dalším krokem, dokud Azure nedokončí vytváření virtuálního počítače.

## <a name="test-traffic-filters"></a>Testování filtrů provozu

Pomocí následujícího příkazu vytvořte relaci SSH s virtuálním virtuálním mem *myVmMgmt.* Nahraďte * \<>publicIpAddress* veřejnou IP adresou virtuálního počítače. Ve výše uvedeném příkladu je adresa IP *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Po zobrazení výzvy k zadání hesla zadejte heslo, které jste zadali v okně [Vytvořit virtuální počítače](#create-virtual-machines).

Připojení je úspěšné, protože port 22 je povolen příchozí z Internetu do skupiny zabezpečení aplikace *myAsgMgmtServers,* ve které je síťové rozhraní připojené k virtuálnímu modulu *myVmMgmt.*

Použijte následující příkaz s ssh na *myVmWeb* VM z *myVmMgmt* virtuálního virtuálního mísy:

```bash 
ssh azureuser@myVmWeb
```

Připojení bude úspěšné, protože výchozí pravidlo zabezpečení v obou skupinách zabezpečení sítě povoluje provoz na všech portech mezi všemi IP adresami v rámci virtuální sítě. Nelze SSH na *myVmWeb* VM z Internetu, protože pravidlo zabezpečení pro *myAsgWebServers* neumožňuje port 22 příchozí z Internetu.

K instalaci webového serveru nginx na virtuální ms *myVmWeb* použijte následující příkazy:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Virtuální ho ms *myVmWeb* je povoleno odchozí do Internetu načíst nginx, protože výchozí pravidlo zabezpečení umožňuje veškerý odchozí provoz do Internetu. Ukončete relaci *myVmWeb* SSH, `username@myVmMgmt:~$` která vás ponechá na výzvu virtuálního zařízení *myVmMgmt.* Chcete-li načíst uvítací obrazovku nginx z virtuálního virtuálního serveru *myVmWeb,* zadejte následující příkaz:

```bash
curl myVmWeb
```

Odhlášení z virtuálního virtuálního mísy *myVmMgmt.* Chcete-li potvrdit, že máte přístup k webovému serveru `curl <publicIpAddress>` *myVmWeb* mimo Azure, zadejte z vlastního počítače. Připojení je úspěšné, protože port 80 je povolen příchozí z Internetu do skupiny zabezpečení aplikace *myAsgWebServers,* ve které je síťové rozhraní připojené k virtuálnímu zařízení *myVmWeb.*

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, použijte [az skupiny odstranit](/cli/azure/group) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili skupinu zabezpečení sítě a přidružili ji k podsíti virtuální sítě. Další informace o skupinách zabezpečení sítě najdete v tématech [Přehled skupin zabezpečení sítě](security-overview.md) a [Správa skupiny zabezpečení sítě](manage-network-security-group.md).

Provoz mezi podsítěmi směruje ve výchozím nastavení Azure. Místo toho se můžete rozhodnout směrovat provoz mezi podsítěmi například prostřednictvím virtuálního počítače, který slouží jako brána firewall. Informace o tom, jak [najdete v tématu Vytvoření tabulky tras](tutorial-create-route-table-cli.md).
