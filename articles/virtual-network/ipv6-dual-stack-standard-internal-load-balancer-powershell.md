---
title: Nasazení aplikace iPv6 se dvěma zásobníky pomocí standardního interního vytápěče zatížení v Azure – PowerShell
titlesuffix: Azure Virtual Network
description: Tento článek ukazuje, jak nasadit aplikaci IPv6 dual stack se standardním interním nástrojem pro vyrovnávání zatížení ve virtuální síti Azure pomocí Azure Powershellu.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: fdf726fd31e8b92a04a1c136eb5cd7110e0c6d5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72333362"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>Nasazení aplikace iPv6 se dvěma zásobníky pomocí standardního interního vytažovače zatížení v Azure – PowerShell (preview)

Tento článek ukazuje, jak nasadit aplikaci s duálním zásobníkem (IPv4 + IPv6) v Azure, která zahrnuje virtuální síť a podsíť se dvěma zásobníky, standardní interní nástroj pro vyrovnávání zatížení s duálními konfiguracemi front-endů (IPv4 + IPv6), virtuální počítače s připojením k síti K), které mají duální IP adresu konfigurace, skupiny zabezpečení sítě a veřejných IP adresy.

> [!Important]
> Podpora IPv6 pro virtuální síť Azure je momentálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Postup vytvoření interního vydělávače zatížení podporujícího protokol IPv6 je téměř totožný s procesem vytvoření internetového vykladače zatížení IPv6, který [je popsán zde](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md). Jediné rozdíly pro vytvoření interního nástroje pro vyrovnávání zatížení jsou v konfiguraci front-endu, jak je znázorněno v příkladu prostředí PowerShell níže:

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "ace:cab:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

Změny, které dělají výše uvedenou konfiguraci front-endu nástroje pro vyrovnávání zatížení, jsou:
- Je `PrivateIpAddressVersion` určen jako "IPv6"
- Argument `-PublicIpAddress` byl vynechán nebo nahrazen písmenem `-PrivateIpAddress`. Všimněte si, že privátní adresa musí být v rozsahu prostoru IP podsítě, ve kterém bude nasazen interní systém vyrovnávání zatížení. Pokud je `-PrivateIpAddress` vynechána statická, bude další volná adresa IPv6 vybrána z podsítě, ve které je nasazen interní systém vyrovnávání zatížení.
- Podsíť s duálním zásobníkem, ve které bude nasazen `-Subnet` `-SubnetId` interní systém vyrovnávání zatížení, je určena buď argumentem nebo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, tento článek vyžaduje modul Azure PowerShell verze 6.9.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="prerequisites"></a>Požadavky
Před nasazením aplikace se dvěma zásobníky v Azure je nutné nakonfigurovat předplatné pro tuto funkci náhledu pomocí následujícího Prostředí Azure PowerShell:

Zaregistrujte se takto:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Dokončení registrace funkce trvá až 30 minut. Stav registrace můžete zkontrolovat spuštěním následujícího příkazu Azure PowerShell: Zkontrolujte registraci takto:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Po dokončení registrace spusťte následující příkaz:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením virtuální sítě se dvěma zásobníky je nutné vytvořit skupinu prostředků pomocí [skupiny New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *dsStd_ILB_RG* v *umístění východní nás:*

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Vytvoření veřejných IP adres IPv4 a IPv6
Chcete-li získat přístup k virtuálním počítačům z Internetu, potřebujete veřejné IP adresy IPv4 a IPv6 pro virtuální počítače. Vytvořte veřejné IP adresy s [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Následující příklad vytvoří veřejnou IP adresu IPv4 a IPv6 s názvem *RdpPublicIP_1* a *RdpPublicIP_2* ve skupině *prostředků dsStd_ILB_RG:*

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>Vytvoření virtuální sítě a podsítě

Vytvořte virtuální síť pomocí [new-azvirtualnetwork](/powershell/module/az.network/new-azvirtualnetwork) s duální množiny konfigurace podsítě pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Následující příklad vytvoří virtuální síť s názvem *dsVnet* s *dsSubnet*.

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>Vytvoření Load Balanceru úrovně Standard

V této části nakonfigurujete duální frontendovou IP adresu (IPv4 a IPv6) a fond back-endových adres pro vykladač zatížení a pak vytvoříte standardní vyvažovač zatížení.

### <a name="create-front-end-ip"></a>Vytvořit ip adresu front-endu

Vytvořte front-end IP s [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Následující příklad vytvoří front-endové konfigurace IP protokolu IPv4 a IPv6 s názvem *dsLbFrontEnd_v4* a *dsLbFrontEnd_v6*:

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "ace:cab:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>Konfigurace fondu adres back-end

Vytvořte back-end ový fond adres pomocí [new-azloadbalancerbackendaddressconfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Virtuální virtuální chod připojit k tomuto fondu back-end ve zbývajících krocích. Následující příklad vytvoří fondy back-endových adres s názvem *dsLbBackEndPool_v4* a *dsLbBackEndPool_v6* tak, aby zahrnovaly virtuální počítače s konfiguracemi inic IPV4 i IPv6:

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Chcete-li zajistit, aby pouze virtuální chody v pořádku přijímat provoz, můžete volitelně definovat sondu stavu. Základní správce zatížení používá sondu IPv4 k posouzení stavu pro koncové body IPv4 i IPv6 na virtuálních počítačích. Standardní vykladač zatížení zahrnuje podporu pro explicitní sondy stavu IPv6.

Vytvořte pravidlo pro vyrovnávání zatížení pomocí [doplňku Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Následující příklad vytvoří pravidla nástroje pro vyrovnávání zatížení s názvem *dsLBrule_v4* a *dsLBrule_v6* a vyváží provoz na portu *TCP* *80* s front-endovými konfiguracemi IP protokolu IPv4 a IPv6:

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte standardní vyvažovač zatížení s [new-azloadbalancer](/powershell/module/az.network/new-azloadbalancer). Následující příklad vytvoří veřejný standardní nástroj pro vyrovnávání zatížení s názvem *myInternalLoadBalancer* pomocí front-endových konfigurací IP IPv4 a IPv6, back-endových fondů a pravidel vyrovnávání zatížení, které jste vytvořili v předchozích krocích:

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků
Před nasazením některých virtuálních počítačů a můžete otestovat vyvažování, musíte vytvořit podpůrné síťové prostředky – dostupnost set, skupiny zabezpečení sítě a virtuální síťové karty. 

### <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti
Chcete-li zlepšit vysokou dostupnost vaší aplikace, umístěte virtuální počítače do sady dostupnosti.

Vytvořte sadu dostupnosti s [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). Následující příklad vytvoří sadu dostupnosti s názvem *dsAVset*:

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pro pravidla, která se bude řídit příchozí a odchozí komunikace ve vaší virtuální síti.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Vytvoření pravidla skupiny zabezpečení sítě pro port 3389

Vytvořte pravidlo skupiny zabezpečení sítě, které umožní připojení protokolu RDP prostřednictvím portu 3389 pomocí [programu New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleRDP' `
  -Description 'Allow RDP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Vytvoření pravidla skupiny zabezpečení sítě pro port 80

Vytvořte pravidlo skupiny zabezpečení sítě, které umožní připojení k Internetu prostřednictvím portu 80 pomocí [aplikace New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pomocí skupiny [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>Vytvoření síťových rozhraní

Vytvořte virtuální síťové karty s [rozhraním New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Následující příklad vytvoří dvě virtuální síťové karty s konfiguracemi IPv4 a IPv6. (Jednu virtuální síťovou kartu pro každý virtuální počítač, který pro svou aplikaci vytvoříte v následujících krocích).

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálních počítačů:

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

Teď můžete vytvářet virtuální hospova s [New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří dva virtuální počítače a požadované součásti virtuální sítě, pokud ještě neexistují.

```azurepowershell
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1


$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Zobrazení virtuální sítě iPv6 dual stack na webu Azure Portal
Virtuální síť IPv6 dual stack můžete zobrazit na webu Azure Portal následujícím způsobem:
1. Na vyhledávacím panelu portálu zadejte *dsVnet*.
2. Když se ve výsledcích hledání zobrazí **dsVnet,** vyberte ji. Tím se spustí stránka **Přehled** virtuální sítě s názvem dsVnet s názvem *dsVnet*. Virtuální síť se dvěma zásobníky zobrazuje dvě síťové karty s konfiguracemi IPv4 i IPv6 umístěnými v podsíti s duálním zásobníkem s názvem *dsSubnet*.

![Virtuální síť IPv6 Dual Stack se standardním interním nástrojem pro vyrovnávání zatížení](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> Virtuální síť IPv6 pro Azure je dostupná na webu Azure Portal jen pro čtení pro tuto předběžnou verzi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, můžete použít příkaz [Odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) k odebrání skupiny prostředků, virtuálního virtuálního času a všech souvisejících prostředků.

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili standardní nástroj pro vyrovnávání zatížení s konfigurací protokolu IP s duálním front-endem (IPv4 a IPv6). Vytvořili jste také dva virtuální počítače, které zahrnovaly síťové karty s konfigurací duální IP adresy (IPV4 + IPv6), které byly přidány do back-endového fondu nástroje pro vyrovnávání zatížení. Další informace o podpoře IPv6 ve virtuálních sítích Azure najdete v tématu [Co je IPv6 pro virtuální síť Azure?](ipv6-overview.md)