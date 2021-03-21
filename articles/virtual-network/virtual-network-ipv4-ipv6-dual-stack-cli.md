---
title: Nasazení IPv6 duální aplikace – Basic Load Balancer-CLI
titlesuffix: Azure Virtual Network
description: Naučte se, jak nasadit aplikaci duálního zásobníku (IPv4 + IPv6) se základní Load Balancer pomocí Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: e9bb0f499e8df712107a6fcdbff14ee367a02bf4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934153"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli"></a>Nasazení duální aplikace s protokolem IPv6 pomocí základního Load Balancer-CLI

V tomto článku se dozvíte, jak nasadit aplikaci duálního zásobníku (IPv4 + IPv6) se základními Load Balancer pomocí rozhraní příkazového řádku Azure, které zahrnuje virtuální síť s duálním zásobníkem s podsítí duálního zásobníku, základní Load Balancer s front-end konfigurací Dual (IPv4 + IPv6), virtuální počítače se síťovými kartami s konfigurací duální sítě a duální veřejné IP adresy.

Postup nasazení aplikace s duálním zásobníkem (IPV4 + IPv6) pomocí Standard Load Balancer najdete v tématu [nasazení duální aplikace s protokolem IPv6 s Standard Load Balancer pomocí Azure CLI](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.49 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Než budete moct vytvořit virtuální síť se dvěma zásobníky, musíte vytvořit skupinu prostředků pomocí [AZ Group Create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *DsResourceGroup01* v umístění *eastus* :

```azurecli-interactive
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Vytvoření veřejných IP adres IPv4 a IPv6 pro nástroj pro vyrovnávání zatížení
Pro přístup k koncovým bodům IPv4 a IPv6 na internetu potřebujete veřejné IP adresy protokolů IPv4 a IPv6 pro nástroj pro vyrovnávání zatížení. Vytvořte veřejnou IP adresu pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip). Následující příklad vytvoří veřejnou IP adresu IPv4 a IPv6 s názvem *dsPublicIP_v4* a *dsPublicIP_v6* ve skupině prostředků *DsResourceGroup01* :

```azurecli-interactive
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

## <a name="create-public-ip-addresses-for-vms"></a>Vytváření veřejných IP adres pro virtuální počítače

Pro vzdálený přístup k virtuálním počítačům na internetu potřebujete pro virtuální počítače veřejné IP adresy IPv4. Vytvořte veřejnou IP adresu pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
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

V této části nakonfigurujete pro nástroj pro vyrovnávání zatížení duální front-end IP adresu (IPv4 a IPv6) a fond back-endové adresy a pak vytvoříte základní Load Balancer.

### <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte základní Load Balancer pomocí [AZ Network](/cli/azure/network/lb) Create s názvem **dsLB** , který zahrnuje front-endu s názvem **dsLbFrontEnd_v4**, back-end fond s názvem **dsLbBackEndPool_v4** , který je přidružený k veřejné IP adrese IPv4 **dsPublicIP_v4** , kterou jste vytvořili v předchozím kroku. 

```azurecli-interactive
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Vytvořit front-end IPv6

Vytvořte IP front-endu protokolu IPV6 pomocí [AZ Network disendu-IP Create](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create). Následující příklad vytvoří konfiguraci IP adresy front-endu s názvem *dsLbFrontEnd_v6* a připojí *dsPublicIP_v6* adresu:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Konfigurace fondu adres back-endu protokolu IPv6

Vytvořte fondy adres back-endu IPv6 pomocí [AZ Network disrovnává Address-Pool Create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create). V následujícím příkladu se vytvoří fond back-endu s názvem *dsLbBackEndPool_v6*  pro zahrnutí virtuálních počítačů s KONFIGURACEMI síťových adaptérů IPv6:

```azurecli-interactive
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu
Pomocí příkazu [az network lb probe create](/cli/azure/network/lb/probe) vytvořte sondu stavu pro monitorování stavu virtuálních počítačů. 

```azurecli-interactive
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí příkazu [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create). Následující příklad vytvoří pravidla nástroje pro vyrovnávání zatížení s názvem *dsLBrule_v4* a *dsLBrule_v6* a vyrovnává provoz na portu *TCP* *80* s konfiguracemi IP adres IPv4 a IPv6 front-endu:

```azurecli-interactive
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků
Před nasazením některých virtuálních počítačů je nutné vytvořit podpůrné síťové prostředky – skupinu dostupnosti, skupinu zabezpečení sítě, virtuální síť a virtuální síťové karty. 
### <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti
Pokud chcete zlepšit dostupnost vaší aplikace, umístěte virtuální počítače do skupiny dostupnosti.

Vytvořte skupinu dostupnosti pomocí [AZ VM Availability-set Create](/cli/azure/vm/availability-set). Následující příklad vytvoří skupinu dostupnosti s názvem *dsAVset*:

```azurecli-interactive
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

Vytvořte skupinu zabezpečení sítě pomocí [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create) .


```azurecli-interactive
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Vytvoření pravidla skupiny zabezpečení sítě pro příchozí a odchozí připojení

Vytvořte pravidlo skupiny zabezpečení sítě, které povolí připojení RDP přes port 3389, připojení k internetu přes port 80 a pro odchozí připojení pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

```azurecli-interactive
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

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Následující příklad vytvoří virtuální síť s názvem *dsVNET* s podsítěmi *dsSubNET_v4* a *dsSubNET_v6*:

```azurecli-interactive
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "fd00:db8:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "fd00:db8:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Vytvoření síťových rozhraní

Pro každý virtuální počítač vytvořte virtuální síťové adaptéry pomocí [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create). Následující příklad vytvoří virtuální síťovou kartu pro každý virtuální počítač. Každé síťové rozhraní má dvě konfigurace protokolu IP (1 konfigurace IPv4, 1 konfigurace IPv6). Konfiguraci protokolu IPV6 vytvoříte pomocí [AZ Network nic IP-config Create](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-create).

```azurecli-interactive
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

Vytvořte virtuální počítače pomocí [AZ VM Create](/cli/azure/vm#az-vm-create). Následující příklad vytvoří dva virtuální počítače a požadované součásti virtuální sítě, pokud ještě neexistují. 

*DsVM0* virtuálního počítače vytvoříte takto:

```azurecli-interactive
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

*DsVM1* virtuálního počítače vytvoříte takto:

```azurecli-interactive
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Zobrazení virtuální sítě s duálním zásobníkem IPv6 v Azure Portal
Virtuální síť s duálním zásobníkem IPv6 se dá zobrazit v Azure Portal následujícím způsobem:
1. Na panelu hledání na portálu zadejte *dsVnet*.
2. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji. Tím se spustí Stránka s **přehledem** pro virtuální síť Dual stack s názvem *dsVnet*. Virtuální síť Dual Stack zobrazuje dvě síťové karty s konfiguracemi protokolů IPv4 i IPv6 umístěných v podsíti duálního zásobníku s názvem *dsSubnet*.

  ![Virtuální síť s duálním zásobníkem IPv6 v Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili základní Load Balancer s duální konfigurací IP adresy front-endu (IPv4 a IPv6). Vytvořili jste také dva virtuální počítače, které obsahovaly síťové adaptéry s konfiguracemi duálních IP adres (IPV4 + IPv6), které byly přidány do fondu back-end nástroje pro vyrovnávání zatížení. Další informace o podpoře IPv6 ve virtuálních sítích Azure najdete v tématu [co je IPv6 pro Azure Virtual Network?](ipv6-overview.md)
