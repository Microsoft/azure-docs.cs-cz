---
title: Ukázkový skript Azure Powershellu – koncové body IPv6 konfigurace virtuální sítě (preview)
titlesuffix: Azure Virtual Network
description: Povolit koncové body IPv6 pomocí prostředí Powershell v Azure Virtual Network
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 87c80857a1d4321768d9e0cdefa48d06c4d16a52
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761476"
---
# <a name="configure-ipv6-endpoints-in-virtual-network-script-sample-preview"></a>Konfigurace koncových bodů protokolu IPv6 v ukázkový skript virtuální sítě (preview)

V tomto článku se dozvíte, jak nasadit aplikaci duálním zásobníkem (IPv4 + IPv6) v Azure, která zahrnuje virtuální síť duálním zásobníkem s duálním zásobníkem podsítí, nástroj pro vyrovnávání zatížení s front-endové konfigurace dvou (IPv4 + IPv6), virtuální počítače se síťovými kartami, které mají duální konfigurace protokolu IP pravidla skupiny zabezpečení sítě duální a duální veřejné IP adresy.

Skript můžete spustit ve službě Azure [Cloud Shell](https://shell.azure.com/powershell) nebo v místně nainstalovaném PowerShellu. Pokud používáte PowerShell místně, vyžaduje tento skript Az modul Azure PowerShell verze 1.0.0 nebo novějším. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky
Před nasazením duálním zásobníkem aplikace v Azure, je nutné nakonfigurovat předplatné pouze jednou pro tuto funkci verze preview pomocí Azure Powershellu následující:

Zaregistrujte následujícím způsobem:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
Trvá až 30 minut, než funkce registraci dokončit. Spuštěním následujícího příkazu prostředí Azure PowerShell, můžete zkontrolovat stav registrace: Kontrola registrace následujícím způsobem:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
Po dokončení registrace, spusťte následující příkaz:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="sample-script"></a>Ukázkový skript



```azurepowershell
# Dual-Stack VNET with 2 VMs.ps1
#   Deploys Dual-stack (IPv4+IPv6) Azure virtual network with 2 VMs and Basic Load Balancer with IPv4 and IPv6 public IPs

# Create resource group to contain the deployment
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"

# Create the public IPs needed for the deployment
  $PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6

  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
    $RdpPublicIP_2 = New-AzPublicIpAddress `
    -Name "RdpPublicIP_2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -AllocationMethod Dynamic `
    -IpAddressVersion IPv4


# Create Basic Load Balancer
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
  
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
  -Name "dsLbBackEndPool_v4"
$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
  -Name "dsLbBackEndPool_v6"

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
  
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

# Create availability set
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
  
# Create network security group and rules
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

$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2

#Create virtual network and subnet
# Create dual stack subnet config
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $subnet
  
  #Create network interfaces (NICs)
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
    
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 



# Create virtual machines
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs"

$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2016-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2


#End Of Script

```
## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky:

```powershell
Remove-AzResourceGroup -Name <resourcegroupname> -Force
```
## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače, skupiny dostupnosti, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Vytvoří konfiguraci podsítě. Tato konfigurace se použije v procesu vytváření virtuální sítě. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť Azure a podsíť. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)  | Vytvoří veřejnou IP adresu se statickou IP adresou a přidruženým názvem DNS. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)  | Vytvoří nástroj pro vyrovnávání zatížení Azure. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Vytvoří sondu nástroje pro vyrovnávání zatížení. Sonda nástroje pro vyrovnávání zatížení se používá k monitorování jednotlivých virtuálních počítačů v sadě nástroje pro vyrovnávání zatížení. Pokud nějaký virtuální počítač přestane být přístupný, nebude se do něj směrovat provoz. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Vytvoří pravidlo nástroje pro vyrovnávání zatížení. V této ukázce se vytvoří pravidlo pro port 80. Když nástroj pro vyrovnávání zatížení přijme přenos HTTP, přesměruje ho na port 80 některého z virtuálních počítačů v sadě nástroje pro vyrovnávání zatížení. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Vytvoří pravidlo překladu adres (NAT) nástroje pro vyrovnávání zatížení.  Pravidla NAT mapují port nástroje pro vyrovnávání zatížení na port na virtuálním počítači. V této ukázce se vytvoří pravidlo NAT pro provoz SSH do všech virtuálních počítačů v sadě nástroje pro vyrovnávání zatížení.  |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Vytvoří skupinu zabezpečení sítě (NSG), což je hranice zabezpečení mezi internetem a virtuálním počítačem. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Vytvoří pravidlo NSG, které povolí příchozí provoz. V této ukázce se otevře port 22 pro provoz SSH. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Vytvoří virtuální síťovou kartu a připojí ji k virtuální síti, podsíti a skupině NSG. |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | Vytvoří skupinu dostupnosti. Skupiny dostupnosti zajišťují dostupnost aplikace rozmístěním virtuálních počítačů napříč fyzickými prostředky tak, že pokud dojde k selhání, nemá vliv celá sada. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Vytvoří konfiguraci virtuálního počítače. Tato konfigurace zahrnuje informace, jako je název virtuálního počítače, operační systém a přihlašovací údaje pro správu. Tato konfigurace se použije při vytváření virtuálního počítače. |
| [New-AzVM](/powershell/module/az.compute/new-azvm)  | Vytvoří virtuální počítač a připojí ho k síťové kartě, virtuální síti, podsíti a skupině NSG. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro sítě najdete v [dokumentaci se základními informacemi k sítím Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
