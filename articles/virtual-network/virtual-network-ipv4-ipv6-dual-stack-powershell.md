---
title: Nasazení aplikace ipv6 duálního zásobníku – základní vyrovnávání zatížení – PowerShell
titlesuffix: Azure Virtual Network
description: Tento článek ukazuje, jak nasadit aplikaci IPv6 dual stack ve virtuální síti Azure pomocí Azure Powershellu.
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
ms.openlocfilehash: 003d677dcdead5792f932ecfe6350df63184cee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75368289"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---powershell-preview"></a>Nasazení aplikace iPv6 se dvěma zásobníky pomocí základního vykladače zatížení – PowerShell (preview)

Tento článek ukazuje, jak nasadit aplikaci s duálním zásobníkem (IPv4 + IPv6) se základním nástrojem pro vyrovnávání zatížení pomocí Azure PowerShellu, který zahrnuje virtuální síť a podsíť se dvěma zásobníky, základní nástroj pro vyrovnávání zatížení s duálními konfiguracemi front-endu (IPv4 + IPv6), virtuální počítače s síťovými kartami, které mají konfiguraci duální IP adresy, skupinu zabezpečení sítě a veřejné IP adresy.

Pokud chcete nasadit aplikaci s duálním zásobníkem (IPV4 + IPv6) pomocí standardního vykladače zatížení, [přečtěte si část Nasazení aplikace iPv6 se standardním vyvažovačem zatížení pomocí Azure PowerShellu](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).

> [!Important]
> Podpora IPv6 pro virtuální síť Azure je momentálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

Před vytvořením virtuální sítě se dvěma zásobníky je nutné vytvořit skupinu prostředků pomocí [skupiny New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myRGDualStack* ve východním umístění *nás:*

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Vytvoření veřejných IP adres IPv4 a IPv6
Chcete-li získat přístup k virtuálním počítačům z Internetu, potřebujete veřejné IP adresy IPv4 a IPv6 pro nástroj pro vyrovnávání zatížení. Vytvořte veřejné IP adresy s [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Následující příklad vytvoří veřejnou IP adresu IPv4 a IPv6 s názvem *dsPublicIP_v4* a *dsPublicIP_v6* ve skupině prostředků *dsRG1:*

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
Chcete-li získat přístup k virtuálním počítačům pomocí připojení RDP, vytvořte veřejné IP adresy IPV4 pro virtuální počítače s [novou azpublicipaddress](/powershell/module/az.network/new-azpublicipaddress).

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

V této části nakonfigurujete duální frontendovou IP adresu (IPv4 a IPv6) a fond back-endových adres pro vykladač zatížení a potom vytvoříte základní vytáčící systém zatížení.

### <a name="create-front-end-ip"></a>Vytvořit ip adresu front-endu

Vytvořte front-end IP s [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Následující příklad vytvoří front-endové konfigurace IP protokolu IPv4 a IPv6 s názvem *dsLbFrontEnd_v4* a *dsLbFrontEnd_v6*:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Konfigurace fondu adres back-end

Vytvořte back-end ový fond adres pomocí [new-azloadbalancerbackendaddressconfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Virtuální virtuální chod připojit k tomuto fondu back-end ve zbývajících krocích. Následující příklad vytvoří fondy back-endových adres s názvem *dsLbBackEndPool_v4* a *dsLbBackEndPool_v6* tak, aby zahrnovaly virtuální počítače s konfiguracemi inic IPV4 i IPv6:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```
### <a name="create-a-health-probe"></a>Vytvoření sondy stavu
Použití [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) k vytvoření sondy stavu pro sledování stavu virtuálních discích.
```azurepowershell
$probe = New-AzLoadBalancerProbeConfig -Name MyProbe -Protocol tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
```
### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Chcete-li zajistit, aby pouze virtuální chody v pořádku přijímat provoz, můžete volitelně definovat sondu stavu. Základní správce zatížení používá sondu IPv4 k posouzení stavu pro koncové body IPv4 i IPv6 na virtuálních počítačích. Standardní vykladač zatížení zahrnuje podporu pro explicitní sondy stavu IPv6.

Vytvořte pravidlo pro vyrovnávání zatížení pomocí [doplňku Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Následující příklad vytvoří pravidla nástroje pro vyrovnávání zatížení s názvem *dsLBrule_v4* a *dsLBrule_v6* a vyváží provoz na portu *TCP* *80* s front-endovými konfiguracemi IP protokolu IPv4 a IPv6:

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

Vytvořte základní vyvažovač zatížení s [new-azloadbalancer](/powershell/module/az.network/new-azloadbalancer). Následující příklad vytvoří veřejný základní nástroj pro vyrovnávání zatížení s názvem *myLoadBalancer* pomocí front-endových konfigurací IP IPv4 a IPv6, back-endových fondů a pravidel vyrovnávání zatížení, které jste vytvořili v předchozích krocích:

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
Před nasazením některých virtuálních počítačů a můžete otestovat vyvažování, musíte vytvořit podpůrné síťové prostředky – dostupnost set, skupiny zabezpečení sítě, virtuální sítě a virtuální síťové karty. 
### <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti
Pokud chcete zlepšit vysokou dostupnost aplikace, umístěte své virtuální počítače do skupiny dostupnosti.

Vytvořte sadu dostupnosti s [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). Následující příklad vytvoří skupinu dostupnosti *myAvailabilitySet*:

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

Vytvořte skupinu zabezpečení sítě pro pravidla, která se bude řídit příchozí a odchozí komunikace ve vaší virtuální nehtové síti.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Vytvoření pravidla skupiny zabezpečení sítě pro port 3389

Vytvořte pravidlo skupiny zabezpečení sítě, které umožní připojení protokolu RDP prostřednictvím portu 3389 pomocí [programu New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

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

Vytvořte pravidlo skupiny zabezpečení sítě, které umožní připojení k Internetu prostřednictvím portu 80 pomocí [aplikace New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

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

Vytvořte skupinu zabezpečení sítě pomocí skupiny [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť s [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVnet* s podsítí *mySubnet*:

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

Vytvořte virtuální síťové karty s [rozhraním New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Následující příklad vytvoří dvě virtuální síťové karty s konfiguracemi IPv4 a IPv6. (Jednu virtuální síťovou kartu pro každý virtuální počítač, který pro svou aplikaci vytvoříte v následujících krocích).

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

Teď můžete vytvářet virtuální hospova s [New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří dva virtuální počítače a požadované součásti virtuální sítě, pokud ještě neexistují. 

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

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>Určení ip adres koncových bodů IPv4 a IPv6
Získejte všechny objekty síťového rozhraní ve skupině prostředků, které `get-AzNetworkInterface`shrnují ip adresy použité v tomto nasazení pomocí aplikace . Také získat front-endové adresy vykladače load balancer koncových bodů `get-AzpublicIpAddress`IPv4 a IPv6 s .

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
Následující obrázek znázorňuje ukázkový výstup, který obsahuje soukromé adresy IPv4 a IPv6 dvou virtuálních počítačů a front-end ip adresy IPv4 a IPv6 v ykreli ipro vykladač ů.

![Souhrn IP adres nasazení aplikací se dvěma zásobníky (IPv4/IPv6) v Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Zobrazení virtuální sítě iPv6 dual stack na webu Azure Portal
Virtuální síť IPv6 dual stack můžete zobrazit na webu Azure Portal následujícím způsobem:
1. Na vyhledávacím panelu portálu zadejte *dsVnet*.
2. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji. Tím se spustí stránka **Přehled** virtuální sítě s názvem dsVnet s názvem *dsVnet*. Virtuální síť se dvěma zásobníky zobrazuje dvě síťové karty s konfiguracemi IPv4 i IPv6 umístěnými v podsíti s duálním zásobníkem s názvem *dsSubnet*.

  ![Virtuální síť s duálním zásobníkem IPv6 v Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Virtuální síť IPv6 pro Azure je dostupná na webu Azure Portal jen pro čtení pro tuto předběžnou verzi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, můžete použít příkaz [Odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) k odebrání skupiny prostředků, virtuálního virtuálního času a všech souvisejících prostředků.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili základní nástroj pro vyrovnávání zatížení s konfigurací protokolu IP s duálním front-endem (IPv4 a IPv6). Vytvořili jste také dva virtuální počítače, které zahrnovaly síťové karty s konfigurací duální IP adresy (IPV4 + IPv6), které byly přidány do back-endového fondu nástroje pro vyrovnávání zatížení. Další informace o podpoře IPv6 ve virtuálních sítích Azure najdete v tématu [Co je IPv6 pro virtuální síť Azure?](ipv6-overview.md)
