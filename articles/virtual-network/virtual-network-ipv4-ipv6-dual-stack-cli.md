---
title: Nasazení aplikace duálním zásobníkem IPv6 ve službě Azure virtual network – rozhraní příkazového řádku
titlesuffix: Azure Virtual Network
description: Tento článek ukazuje, jak nasadit aplikaci duálním zásobníkem IPv6 ve virtuální síti Azure pomocí Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 12fbf2ae5387ac0a9350cc203f4a6f2587c8dafe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130868"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>Nasazení aplikace duálním zásobníkem IPv6 ve virtuální síti Azure - CLI (Preview)

V tomto článku se dozvíte, jak nasadit aplikaci duálním zásobníkem (IPv4 + IPv6) v Azure, která zahrnuje virtuální síť duálním zásobníkem s duálním zásobníkem podsítí, nástroj pro vyrovnávání zatížení s front-endové konfigurace dvou (IPv4 + IPv6), virtuální počítače se síťovými kartami, které mají duální konfigurace protokolu IP pravidla skupiny zabezpečení sítě duální a duální veřejné IP adresy.

> [!Important]
> Duálním zásobníkem protokolu IPv6 pro Azure Virtual Network je aktuálně ve verzi public preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure místně místo toho tento rychlý start vyžaduje použití Azure CLI verze 2.0.49 nebo novější. Nainstalovanou verzi zjistíte spuštěním `az --version`. Zobrazit [instalace Azure CLI](/cli/azure/install-azure-cli) pro instalaci nebo upgradu informace.

## <a name="prerequisites"></a>Požadavky
Použití protokolu IPv6 pro funkci virtuální síť Azure, je nutné nakonfigurovat předplatné pomocí Azure Powershellu takto:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
Trvá až 30 minut, než funkce registraci dokončit. Spuštěním následujícího příkazu rozhraní příkazového řádku Azure, můžete zkontrolovat stav registrace:

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
Po dokončení registrace, spusťte následující příkaz:

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Než vytvoříte virtuální síť duální sadou protokolů, musíte vytvořit skupinu prostředků pomocí [vytvořit skupiny az](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myRGDualStack* v *eastus* umístění:

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Vytvořit protokoly IPv4 a IPv6 veřejné IP adresy nástroje pro vyrovnávání zatížení
Pro přístup k koncové body protokolu IPv4 a IPv6 v síti Internet, musíte protokoly IPv4 a IPv6 veřejné IP adresy nástroje pro vyrovnávání zatížení. Vytvořte veřejnou IP adresu pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip). Následující příklad vytvoří IPv4 a IPv6 veřejnou IP adresu s názvem *dsPublicIP_v4* a *dsPublicIP_v6* v *myRGDualStack* skupina prostředků:

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Vytvoření veřejné IP adresy pro virtuální počítače

Vzdálený přístup k vaší virtuální počítače na Internetu, musíte pro virtuální počítače veřejné IP adresy IPv4. Vytvořte veřejnou IP adresu pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip).

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Vytvoření Load Balanceru úrovně Basic

V této části nakonfigurujete duální front-endové IP adresy (IPv4 a IPv6) a fondu back endových adres nástroje pro vyrovnávání zatížení a pak vytvořte Load balanceru úrovně Basic.

### <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Vytvoření základního Vyrovnávání zatížení s [az network lb vytvořit](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) s názvem **dsLB** , který obsahuje front-endový fond s názvem **dsLbFrontEnd_v4**, back-endový fond s názvem  **dsLbBackEndPool_v4** přidružený k veřejné IP adresy IPv4 **dsPublicIP_v4** , kterou jste vytvořili v předchozím kroku. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Vytvoření front-endu IPv6

Vytvoření front-endová IP protokolu IPV6 s [az sítě lb frontend-ip vytvořit](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). Následující příklad vytvoří konfigurace IP adresy frontendu s názvem *dsLbFrontEnd_v6* a připojí *dsPublicIP_v6* adresa:

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Nakonfigurujte fond back endových adres IPv6

Vytvoření fondů back endových adres IPv6 [az network lb-fondu adres vytvořte](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). Následující příklad vytvoří back endový fond adres s názvem *dsLbBackEndPool_v6* zahrnout virtuální počítače s konfigurací síťové karty IPv6:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Definujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí příkazu [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create). Následující příklad vytvoří pravidla nástroje pro vyrovnávání zatížení s názvem *dsLBrule_v4* a *dsLBrule_v6* a vyrovnává provozu na *TCP* port *80* do protokoly IPv4 a IPv6 front-endová konfigurace protokolu IP:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \ 
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků
Než nasadíte několik virtuálních počítačů, je nutné vytvořit podpůrné síťové prostředky – skupina dostupnosti, skupiny zabezpečení sítě, virtuální síť a virtuální síťové karty. 
### <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti
Pokud chcete zlepšit dostupnost vaší aplikace, umístěte své virtuální počítače ve skupině dostupnosti.

Vytvořte skupinu dostupnosti pomocí příkazu [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). Následující příklad vytvoří skupinu dostupnosti *dsAVset*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pro pravidla, která budou řídit příchozí a odchozí komunikaci ve vaší virtuální síti.

#### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pomocí [vytvořit az network nsg](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Vytvořte pravidlo skupiny zabezpečení sítě pro příchozí a odchozí připojení

Vytvořte pravidlo skupiny zabezpečení sítě umožňující připojení RDP přes port 3389, připojení k Internetu přes port 80 a pro odchozí připojení s [az network nsg pravidlo vytvořte](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

```azurecli
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí příkazu [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). Následující příklad vytvoří virtuální síť s názvem *dsVNET* s podsítěmi *dsSubNET_v4* a *dsSubNET_v6*:

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Vytvoření síťových rozhraní

Vytvořit virtuální síťové karty pro každý virtuální počítač s [az network nic vytvořit](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). Následující příklad vytvoří virtuální síťovou kartu pro každý virtuální počítač. Každé síťové rozhraní se dvěma konfiguracemi IP (1 konfiguraci protokolu IPv4, 1 konfiguraci protokolu IPv6). Vytvořte konfiguraci protokolu IPV6 s [az network nic ip-config vytvořit](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).
 
```azurecli
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvoření virtuálních počítačů s [az vm vytvořit](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). Následující příklad vytvoří dva virtuální počítače a požadované komponenty virtuální sítě, pokud ještě neexistují. 

Vytvoření virtuálního počítače *dsVM0* následujícím způsobem:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest  
```
Vytvoření virtuálního počítače *dsVM1* následujícím způsobem:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Zobrazit virtuální sítě IPv6 duálním zásobníkem na webu Azure portal
Virtuální sítě IPv6 duálním zásobníkem můžete zobrazit na webu Azure portal následujícím způsobem:
1. Do panelu hledání na portálu, zadejte *dsVnet*.
2. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji. Tím se spustí **přehled** stránky duálním zásobníkem virtuální síť s názvem *dsVnet*. Virtuální síť duálním zásobníkem ukazuje dva síťové adaptéry s konfiguracemi protokolu IPv4 a IPv6 v duálním zásobníkem podsíť s názvem *dsSubnet*.

  ![IPv6 duálním zásobníkem virtuální sítě v Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Protokol IPv6 pro virtuální síť Azure je dostupný na webu Azure Portal v režimu jen pro čtení k této verzi preview.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az-group-delete).

```azurecli
 az group delete --name DsRG1
```

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili základní nástroje pro vyrovnávání zatížení s duální front-endu konfigurací IP (IPv4 a IPv6). Také jste vytvořili dva virtuální počítače, které jsou zahrnuté síťové karty s duální konfigurací protokolu IP (IPV4 + IPv6), které byly přidány do back endového fondu nástroje pro vyrovnávání zatížení. Další informace o podpoře IPv6 ve virtuálních sítích Azure najdete v tématu [novinky protokolu IPv6 pro Azure Virtual Network?](ipv6-overview.md)