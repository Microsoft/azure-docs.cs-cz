---
title: Nasazení aplikace ipv6 se dvěma zásobníky – standardní vyrovnávání zatížení – rozhraní CLI
titlesuffix: Azure Virtual Network
description: Tento článek ukazuje, jak nasadit aplikaci IPv6 dual stack ve virtuální síti Azure pomocí rozhraní příkazového příkazového příkazu Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/17/2019
ms.author: kumud
ms.openlocfilehash: fa895a294e26b6c74ab72afa3136feac2b2ec986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240247"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>Nasazení aplikace iPv6 se dvěma zásobníky ve virtuální síti Azure – rozhraní PŘÍKAZOVÉHO KONt (preview)

Tento článek ukazuje, jak nasadit aplikaci s duálním zásobníkem (IPv4 + IPv6) pomocí standardního nástroje pro vyrovnávání zatížení v Azure, která zahrnuje virtuální síť se dvěma zásobníky s podsítí s duálním zásobníkem, standardní nástroj pro vyrovnávání zatížení s duálními konfiguracemi front-endu (IPv4 + IPv6), virtuální počítače s Síťové karty, které mají konfiguraci duální IP adresy, pravidla skupiny zabezpečení dvou sítí a duální veřejné IP adresy.

> [!Important]
> Duální zásobník IPv6 pro virtuální síť Azure je momentálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a použít Azure CLI místně místo, tento rychlý start vyžaduje použití Azure CLI verze 2.0.49 nebo novější. Chcete-li najít nainstalovanou verzi, spusťte program `az --version`. Informace o instalaci nebo upgradu [najdete v tématu Instalace příkazového příkazového](/cli/azure/install-azure-cli) příkazu Konzumu Azure.

## <a name="prerequisites"></a>Požadavky
Chcete-li použít funkci virtuální sítě IPv6 pro Azure, musíte předplatné nakonfigurovat pomocí rozhraní příkazového příkazového příkazu Azure následujícím způsobem:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

Dokončení registrace funkce trvá až 30 minut. Stav registrace můžete zkontrolovat spuštěním následujícího příkazu Azure CLI:

```azurecli
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

Po dokončení registrace spusťte následující příkaz:

```azurecli
az provider register --namespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením virtuální sítě se dvěma zásobníky je nutné vytvořit skupinu prostředků s [vytvořením skupiny az](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *DsResourceGroup01* v umístění *eastus:*

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Vytvoření veřejných IP adres IPv4 a IPv6 pro vykladač zatížení
Chcete-li získat přístup ke koncovým bodům IPv4 a IPv6 v Internetu, potřebujete pro vykladač zatížení iPv4 a IPv6 veřejné IP adresy. Vytvořte veřejnou IP adresu pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip). Následující příklad vytvoří veřejnou IP adresu IPv4 a IPv6 s názvem *dsPublicIP_v4* a *dsPublicIP_v6* ve skupině prostředků *DsResourceGroup01:*

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku STANDARD  \
--allocation-method static  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku STANDARD  \
--allocation-method static  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Vytvoření veřejných IP adres pro virtuální hospo-

Chcete-li vzdáleně přistupovat k virtuálním počítačům na internetu, potřebujete iPv4 veřejné IP adresy pro virtuální počítače. Vytvořte veřejnou IP adresu pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip).

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4
```

## <a name="create-standard-load-balancer"></a>Vytvoření Load Balanceru úrovně Standard

V této části nakonfigurujete duální frontendovou IP adresu (IPv4 a IPv6) a fond back-endových adres pro vykladač zatížení a pak vytvoříte standardní vyvažovač zatížení.

### <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte standardní vyrovnávání zatížení s [az sítě lb vytvořit](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) s názvem **dsLB,** který obsahuje front-end fond s názvem **dsLbFrontEnd_v4**, back-end fond s názvem **dsLbBackEndPool_v4,** který je spojen s IPv4 veřejné IP adresy **dsPublicIP_v4,** které jste vytvořili v předchozím kroku. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Standard \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Vytvořit front-end IPv6

Create an IPV6 frontend IP with [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). Následující příklad vytvoří front-endovou konfiguraci IP s názvem *dsLbFrontEnd_v6* a připojí *adresu dsPublicIP_v6:*

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Konfigurace fondu back-endových adres IPv6

Vytvořte fondy back-endových adres IPv6 s [vytvořením fondu adres az network lb](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). Následující příklad vytvoří fond back-endových adres s názvem *dsLbBackEndPool_v6* tak, aby zahrnoval virtuální počítače s konfiguracemi iPv6 NIC:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu
Pomocí příkazu [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) vytvořte sondu stavu pro monitorování stavu virtuálních počítačů. 

```azurecli
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí příkazu [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create). Následující příklad vytvoří pravidla nástroje pro vyrovnávání zatížení s názvem *dsLBrule_v4* a *dsLBrule_v6* a vyváží provoz na portu *TCP* *80* s front-endovými konfiguracemi IP protokolu IPv4 a IPv6:

```azurecli
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
Před nasazením některých virtuálních počítačů je nutné vytvořit podpůrné síťové prostředky – sadu dostupnosti, skupinu zabezpečení sítě, virtuální síť a virtuální síťové karty. 
### <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti
Pokud chcete zlepšit dostupnost aplikace, umístěte virtuální počítače do sady dostupnosti.

Vytvořte sadu dostupnosti s [vytvořením sady dostupnosti az vm](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). Následující příklad vytvoří sadu dostupnosti s názvem *dsAVset*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pro pravidla, která se bude řídit příchozí a odchozí komunikace ve vaší virtuální síti.

#### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvoření skupiny zabezpečení sítě pomocí vytvoření [az network nsg](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Vytvoření pravidla skupiny zabezpečení sítě pro příchozí a odchozí připojení

Vytvořte pravidlo skupiny zabezpečení sítě, které umožní připojení PROTOKOLU RDP prostřednictvím portu 3389, připojení k internetu přes port 80 a pro odchozí připojení pomocí [pravidla az network nsg](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

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

Vytvořte virtuální síťové karty pro každý virtuální počítač pomocí [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). Následující příklad vytvoří virtuální nic pro každý virtuální počítač. Každá nic má dvě konfigurace IP (1 konfigurace IPv4, 1 konfigurace IPv6). Konfiguraci IPV6 vytvoříte pomocí vytvoření [az network nic ip-config](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).
 
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

Vytvořte virtuální ms pomocí [vytvoření az vm](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). Následující příklad vytvoří dva virtuální počítače a požadované součásti virtuální sítě, pokud ještě neexistují. 

Vytvořte virtuální počítač *dsVM0* takto:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Vytvořte virtuální počítač *dsVM1* takto:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Zobrazení virtuální sítě iPv6 dual stack na webu Azure Portal
Virtuální síť IPv6 dual stack můžete zobrazit na webu Azure Portal následujícím způsobem:
1. Na vyhledávacím panelu portálu zadejte *dsVnet*.
2. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji. Tím se spustí stránka **Přehled** virtuální sítě s názvem dsVnet s názvem *dsVnet*. Virtuální síť se dvěma zásobníky zobrazuje dvě síťové karty s konfiguracemi IPv4 i IPv6 umístěnými v podsíti s duálním zásobníkem s názvem *dsSubnet*.

  ![Virtuální síť s duálním zásobníkem IPv6 v Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Virtuální síť IPv6 pro Azure je dostupná na webu Azure Portal jen pro čtení pro tuto předběžnou verzi.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete, můžete pomocí příkazu [odstranění skupiny az](/cli/azure/group#az-group-delete) odebrat skupinu prostředků, virtuální hod a všechny související prostředky.

```azurecli
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili standardní nástroj pro vyrovnávání zatížení s konfigurací protokolu IP s duálním front-endem (IPv4 a IPv6). Vytvořili jste také dva virtuální počítače, které zahrnovaly síťové karty s konfigurací duální IP adresy (IPV4 + IPv6), které byly přidány do back-endového fondu nástroje pro vyrovnávání zatížení. Další informace o podpoře IPv6 ve virtuálních sítích Azure najdete v tématu [Co je IPv6 pro virtuální síť Azure?](ipv6-overview.md)
