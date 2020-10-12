---
title: Nasazení duální aplikace pro protokol IPv6 – základní Load Balancer – PowerShell
titlesuffix: Azure Virtual Network
description: Tento článek ukazuje, jak nasadit aplikaci s duálním zásobníkem IPv6 ve službě Azure Virtual Network pomocí Azure PowerShellu.
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
ms.openlocfilehash: 6f51794c28c68b41bb715dcc23ae775151b6d93d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84703056"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---powershell"></a>Nasazení duální aplikace s protokolem IPv6 pomocí základní Load Balancer – PowerShell

V tomto článku se dozvíte, jak nasadit aplikaci duálního zásobníku (IPv4 + IPv6) se základní Load Balancer pomocí Azure PowerShell, která zahrnuje virtuální síť s duálním zásobníkem a podsíť, základní Load Balancer s duálními konfiguracemi (IPv4 + IPv6), virtuální počítače se síťovými kartami, které mají konfiguraci s duálním protokolem IP, skupinu zabezpečení sítě a veřejné IP adresy.

Postup nasazení aplikace s duálním zásobníkem (IPV4 + IPv6) pomocí Standard Load Balancer najdete v tématu [nasazení duální aplikace s protokolem IPv6 s Standard Load Balancer pomocí Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento článek verzi modulu Azure PowerShell 6.9.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Než budete moct vytvořit virtuální síť se dvěma zásobníky, musíte vytvořit skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myRGDualStack* v umístění *východní USA* :

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Vytvoření veřejných IP adres IPv4 a IPv6
Pro přístup k virtuálním počítačům z Internetu potřebujete veřejné IP adresy IPv4 a IPv6 pro nástroj pro vyrovnávání zatížení. Vytvořte veřejné IP adresy pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Následující příklad vytvoří veřejnou IP adresu IPv4 a IPv6 s názvem *dsPublicIP_v4* a *dsPublicIP_v6* ve skupině prostředků *dsRG1* :

```azurepowershell-interactive
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
```
Pro přístup k virtuálním počítačům pomocí připojení RDP vytvořte veřejné IP adresy IPV4 pro virtuální počítače pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
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
```

## <a name="create-basic-load-balancer"></a>Vytvoření Load Balanceru úrovně Basic

V této části nakonfigurujete pro nástroj pro vyrovnávání zatížení duální front-end IP adresu (IPv4 a IPv6) a fond back-endové adresy a pak vytvoříte základní Load Balancer.

### <a name="create-front-end-ip"></a>Vytvoření front-endové IP adresy

Vytvořte front-end IP adresu pomocí [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Následující příklad vytvoří konfigurace IP adresy front-endu IPv4 a IPv6 s názvem *dsLbFrontEnd_v4* a *dsLbFrontEnd_v6*:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Konfigurovat fond back-endu adres

Vytvořte fond back-endové adresy pomocí [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Virtuální počítače se připojují k tomuto fondu back-end ve zbývajících krocích. Následující příklad vytvoří fondy back-endu s názvem *dsLbBackEndPool_v4* a *dsLbBackEndPool_v6* , aby zahrnovaly virtuální počítače s konfigurací síťových adaptérů IPv4 i IPv6:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```
### <a name="create-a-health-probe"></a>Vytvoření sondy stavu
Pomocí [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) vytvořte sondu stavu pro monitorování stavu virtuálních počítačů.
```azurepowershell
$probe = New-AzLoadBalancerProbeConfig -Name MyProbe -Protocol tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
```
### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Chcete-li zajistit, aby provoz přijímal jenom zdravé virtuální počítače, můžete volitelně definovat sondu stavu. Load Balancer úrovně Basic používá ke zhodnocení stavu koncových bodů IPv4 i IPv6 na virtuálních počítačích test paměti IPv4. Load Balancer úrovně Standard zahrnuje podporu pro explicitní testy stavu protokolu IPv6.

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Následující příklad vytvoří pravidla nástroje pro vyrovnávání zatížení s názvem *dsLBrule_v4* a *dsLBrule_v6* a vyrovnává provoz na portu *TCP* *80* s konfiguracemi IP adres IPv4 a IPv6 front-endu:

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe
```

### <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte základní Load Balancer pomocí [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Následující příklad vytvoří veřejný základní Load Balancer s názvem *myLoadBalancer* pomocí konfigurací IPv4 a IPv6 front-endu, back-endu a pravidel vyrovnávání zatížení, které jste vytvořili v předchozích krocích:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků
Před nasazením některých virtuálních počítačů a testováním nástroje pro vyrovnávání zatížení musíte vytvořit podpůrné síťové prostředky – skupinu dostupnosti, skupinu zabezpečení sítě, virtuální síť a virtuální síťové karty. 
### <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti
Pokud chcete zlepšit vysokou dostupnost aplikace, umístěte své virtuální počítače do skupiny dostupnosti.

Vytvořte skupinu dostupnosti pomocí [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). Následující příklad vytvoří skupinu dostupnosti *myAvailabilitySet*:

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pro pravidla, která budou řídit příchozí a odchozí komunikaci ve vaší virtuální síti.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Vytvoření pravidla skupiny zabezpečení sítě pro port 3389

Vytvořte pravidlo skupiny zabezpečení sítě, které povolí připojení RDP přes port 3389 s [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
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

Vytvořte pravidlo skupiny zabezpečení sítě, které povolí připojení k Internetu prostřednictvím portu 80 s [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
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

Vytvořte skupinu zabezpečení sítě pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVnet* s podsítí *mySubnet*:

```azurepowershell-interactive
# Create dual stack subnet
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
```

### <a name="create-nics"></a>Vytvoření síťových rozhraní

Vytvořte virtuální síťové karty pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Následující příklad vytvoří dvě virtuální síťové karty s konfiguracemi protokolů IPv4 a IPv6. (Jednu virtuální síťovou kartu pro každý virtuální počítač, který pro svou aplikaci vytvoříte v následujících krocích).

```azurepowershell-interactive
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

```

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálních počítačů:

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

Nyní můžete vytvořit virtuální počítače pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří dva virtuální počítače a požadované součásti virtuální sítě, pokud ještě neexistují. 

```azurepowershell-interactive
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

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>Určete IP adresy koncových bodů IPv4 a IPv6.
Získejte všechny objekty síťového rozhraní ve skupině prostředků, abyste mohli shrnout IP adresu použitou v tomto nasazení s `get-AzNetworkInterface` . Získejte také adresy front-endu Load Balancer koncových bodů IPv4 a IPv6 `get-AzpublicIpAddress` .

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
Následující obrázek ukazuje vzorový výstup, který obsahuje privátní IPv4 a IPv6 adresy dvou virtuálních počítačů a IP adresy front-endu IPv4 a IPv6 Load Balancer.

![Souhrn IP adres nasazení aplikace Dual Stack (IPv4/IPv6) v Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Zobrazení virtuální sítě s duálním zásobníkem IPv6 v Azure Portal
Virtuální síť s duálním zásobníkem IPv6 se dá zobrazit v Azure Portal následujícím způsobem:
1. Na panelu hledání na portálu zadejte *dsVnet*.
2. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji. Tím se spustí Stránka s **přehledem** pro virtuální síť Dual stack s názvem *dsVnet*. Virtuální síť Dual Stack zobrazuje dvě síťové karty s konfiguracemi protokolů IPv4 i IPv6 umístěných v podsíti duálního zásobníku s názvem *dsSubnet*.

  ![Virtuální síť s duálním zásobníkem IPv6 v Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili základní Load Balancer s duální konfigurací IP adresy front-endu (IPv4 a IPv6). Vytvořili jste také dva virtuální počítače, které obsahovaly síťové adaptéry s konfiguracemi duálních IP adres (IPV4 + IPv6), které byly přidány do fondu back-end nástroje pro vyrovnávání zatížení. Další informace o podpoře IPv6 ve virtuálních sítích Azure najdete v tématu [co je IPv6 pro Azure Virtual Network?](ipv6-overview.md)
