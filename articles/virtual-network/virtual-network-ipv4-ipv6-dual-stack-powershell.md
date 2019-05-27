---
title: Nasazení aplikace duálním zásobníkem IPv6 ve službě Azure virtual network – PowerShell
titlesuffix: Azure Virtual Network
description: Tento článek ukazuje, jak nasadit aplikaci duálním zásobníkem IPv6 ve virtuální síti Azure pomocí Azure Powershellu.
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
ms.openlocfilehash: a9f8066896134072665c3f5b325e033b638bf094
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000998"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---powershell-preview"></a>Nasazení aplikace duálním zásobníkem protokolu IPv6 v Azure – prostředí PowerShell (Preview)

V tomto článku se dozvíte, jak nasadit aplikaci duálním zásobníkem (IPv4 + IPv6) v Azure, která zahrnuje duálním zásobníkem virtuální sítě a podsítě, nástroj pro vyrovnávání zatížení s front-endové konfigurace dvou (IPv4 + IPv6), virtuální počítače se síťovými kartami, které mají duální konfigurace protokolu IP sítě skupiny zabezpečení a veřejné IP adresy.

> [!Important]
> Podpora protokolu IPv6 pro Azure Virtual Network je aktuálně ve verzi public preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte modul Azure PowerShell verze 6.9.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="prerequisites"></a>Požadavky
Před nasazením duálním zásobníkem aplikace v Azure, musí konfigurace předplatného pro tuto funkci verze preview pomocí Azure Powershellu následující:

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

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Než vytvoříte virtuální síť duální sadou protokolů, musíte vytvořit skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myRGDualStack* v *Východ USA* umístění:

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Vytvořit protokoly IPv4 a IPv6 veřejné IP adresy
Pro přístup k virtuální počítače z Internetu, musíte protokoly IPv4 a IPv6 veřejné IP adresy nástroje pro vyrovnávání zatížení. Vytvoření veřejné IP adresy s [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Následující příklad vytvoří IPv4 a IPv6 veřejnou IP adresu s názvem *dsPublicIP_v4* a *dsPublicIP_v6* v *dsRG1* skupina prostředků:

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
Pro přístup k virtuální počítače pomocí připojení RDP, vytvořte veřejné IP adresy IPV4 pro virtuální počítače s [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

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

V této části nakonfigurujete duální front-endové IP adresy (IPv4 a IPv6) a fondu back endových adres nástroje pro vyrovnávání zatížení a pak vytvořte Load balanceru úrovně Basic.

### <a name="create-front-end-ip"></a>Vytvoření front-endové IP adresy

Vytvoření front-endové IP adresy s [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Následující příklad vytvoří front-endu IPv4 a IPv6, konfigurace IP adresy s názvem *dsLbFrontEnd_v4* a *dsLbFrontEnd_v6*:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Konfigurace back endového fondu adres

Vytvořte fond back endových adres s [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Virtuální počítače připojit k tomuto fondu back-end ve zbývajících krocích. Následující příklad vytvoří fondy adres back endu s názvem *dsLbBackEndPool_v4* a *dsLbBackEndPool_v6* zahrnout virtuální počítače s protokoly IPV4 a IPv6 NIC konfigurace:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Definujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Ujistěte se, že pouze v dobrém stavu virtuálních počítačů příjem přenosů, Volitelně můžete definovat sondy stavu. Load balanceru úrovně Basic používá k vyhodnocení stavu pro koncové body protokolu IPv4 a IPv6 na virtuálních počítačích sondu protokolu IPv4. Load balancer úrovně Standard zahrnuje podporu pro explicitně sondy stavu protokolu IPv6.

Vytvořte pravidlo nástroje pro vyrovnávání zatížení s [přidat AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Následující příklad vytvoří pravidla nástroje pro vyrovnávání zatížení s názvem *dsLBrule_v4* a *dsLBrule_v6* a vyrovnává provozu na *TCP* port *80* do protokoly IPv4 a IPv6 front-endová konfigurace protokolu IP:

```azurepowershell-interactive
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

### <a name="create-load-balancer"></a>Vytvořit nástroj pro vyrovnávání zatížení

Vytvořte Load balanceru úrovně Basic s [nové AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Následující příklad vytvoří veřejný Load balancer úrovně Basic s názvem *myLoadBalancer* pomocí front-endové IP adresy IPv4 a IPv6 konfigurace back-endové fondy a pravidla Vyrovnávání zatížení, které jste vytvořili v předchozích krocích:

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
Než nasadíte několik virtuálních počítačů a otestovat váš nástroj pro vyrovnávání podpůrné síťové prostředky – musíte vytvořit skupinu dostupnosti, skupiny zabezpečení sítě, virtuální síť a virtuální síťové karty. 
### <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti
Pokud chcete zlepšit vysokou dostupnost aplikace, umístěte své virtuální počítače do skupiny dostupnosti.

Vytvořit skupinu dostupnosti pomocí [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). Následující příklad vytvoří skupinu dostupnosti s názvem *myAvailabilitySet*:

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Vytvořit skupinu zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pro pravidla, která budou řídit příchozí a odchozí komunikaci ve vaší virtuální síti.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Vytvoření pravidla skupiny zabezpečení sítě pro port 3389

Vytvořte pravidlo skupiny zabezpečení sítě umožňující připojení RDP přes port 3389 s [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

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

Vytvořte pravidlo skupiny zabezpečení sítě umožňující připojení k Internetu přes port 80 s [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

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

Vytvoření virtuální sítě s [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVnet* s podsítí *mySubnet*:

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

Vytvořit virtuální síťové karty s [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Následující příklad vytvoří dvě virtuální síťové adaptéry i s konfiguracemi protokolu IPv4 a IPv6. (Jednu virtuální síťovou kartu pro každý virtuální počítač, který pro svou aplikaci vytvoříte v následujících krocích).

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

Nyní můžete vytvořit virtuální počítače s [rutiny New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří dva virtuální počítače a požadované komponenty virtuální sítě, pokud ještě neexistují. 

```azurepowershell-interactive
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
```

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>Určení IP adresami koncových bodů protokolu IPv4 a IPv6
Získat všechny objekty rozhraní sítě ve skupině prostředků slouží ke shrnutí IP adresy použité v tomto nasazení s `get-AzNetworkInterface`. Získáte také adresy front-endu nástroje pro vyrovnávání zatížení IPv4 a IPv6 koncových bodů s `get-AzpublicIpAddress`.

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
Následující obrázek zobrazuje ukázkový výstup, který obsahuje privátní adresy IPv4 a IPv6 dva virtuální počítače a front-endu IPv4 a IPv6 adres nástroje pro vyrovnávání zatížení.

![IP shrnutí nasazení aplikace duální protokolů (IPv4 nebo IPv6) v Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Zobrazit virtuální sítě IPv6 duálním zásobníkem na webu Azure portal
Virtuální sítě IPv6 duálním zásobníkem můžete zobrazit na webu Azure portal následujícím způsobem:
1. Do panelu hledání na portálu, zadejte *dsVnet*.
2. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji. Tím se spustí **přehled** stránky duálním zásobníkem virtuální síť s názvem *dsVnet*. Virtuální síť duálním zásobníkem ukazuje dva síťové adaptéry s konfiguracemi protokolu IPv4 a IPv6 v duálním zásobníkem podsíť s názvem *dsSubnet*.

  ![IPv6 duálním zásobníkem virtuální sítě v Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Protokol IPv6 pro virtuální síť Azure je dostupný na webu Azure Portal v režimu jen pro čtení k této verzi preview.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete použít [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) příkazu k odebrání skupiny prostředků, virtuálního počítače, a všechny související prostředky.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili základní nástroje pro vyrovnávání zatížení s duální front-endu konfigurací IP (IPv4 a IPv6). Také jste vytvořili dva virtuální počítače, které jsou zahrnuté síťové karty s duální konfigurací protokolu IP (IPV4 + IPv6), které byly přidány do back endového fondu nástroje pro vyrovnávání zatížení. Další informace o podpoře IPv6 ve virtuálních sítích Azure najdete v tématu [novinky protokolu IPv6 pro Azure Virtual Network?](ipv6-overview.md)