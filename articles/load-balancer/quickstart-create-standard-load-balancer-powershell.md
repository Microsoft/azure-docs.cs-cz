---
title: 'Rychlý start: Vytvoření Load balanceru úrovně Standard – Azure PowerShell'
titlesuffix: Azure Load Balancer
description: Tento rychlý start ukazuje, jak pomocí PowerShellu vytvořit Load Balancer úrovně Standard.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: kumud
ms:custom: seodec18
ms.openlocfilehash: 513d3931c31ca94b4089139992bceb6f679caa98
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467695"
---
# <a name="quickstart-create-a-standard-load-balancer-using-azure-powershell"></a>Rychlý start: Vytvořit Standard pro vyrovnávání zatížení pomocí Azure Powershellu

Tento rychlý start ukazuje, jak pomocí Azure PowerShellu vytvořit Load Balancer úrovně Standard. K otestování nástroje pro vyrovnávání zatížení, nasazení tři virtuální počítače (VM) s Windows serverem a webové aplikace mezi virtuálními počítači Vyrovnávání zatížení. Další informace o Load Balanceru úrovně Standard najdete v tématu [Co je Load Balancer úrovně Standard](load-balancer-standard-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Než vytvoříte nástroj pro vyrovnávání zatížení, musíte vytvořit skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupSLB* v *EastUS* umístění:

```azurepowershell
$rgName='MyResourceGroupSLB'
$location='eastus'
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Pokud chcete mít k aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. Vytvoření veřejné IP adresy s [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Následující příklad vytvoří veřejnou IP adresu s názvem *myPublicIP* v *myResourceGroupSLB* skupina prostředků:

```azurepowershell
$publicIp = New-AzPublicIpAddress `
 -ResourceGroupName $rgName `
 -Name 'myPublicIP' `
 -Location $location `
 -AllocationMethod static `
 -SKU Standard
```

## <a name="create-standard-load-balancer"></a>Vytvoření Load Balanceru úrovně Standard

V této části nakonfigurujete front-endové IP adresy a fond back endových adres nástroje pro vyrovnávání zatížení a vytvořte Load balanceru úrovně Standard.

### <a name="create-front-end-ip"></a>Vytvoření front-endové IP adresy

Vytvoření front-endové IP adresy s [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Následující příklad vytvoří konfigurace front-end IP adresy s názvem *myFrontEnd* a připojí *myPublicIP* adresa:

```azurepowershell
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Konfigurace back endového fondu adres

Vytvořte fond back endových adres s [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Virtuální počítače připojit k tomuto fondu back-end ve zbývajících krocích. Následující příklad vytvoří fond back endových adres s názvem *myBackEndPool*:

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu
Pokud chcete nástroji pro vyrovnávání zatížení povolit monitorování stavu vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. Ve výchozím nastavení se virtuální počítač odebere z distribuce nástroje pro vyrovnávání zatížení po dvou selháních po sobě v 15sekundových intervalech. Sondu stavu můžete vytvořit na základě protokolu nebo konkrétní stránky kontroly stavu pro vaši aplikaci.

Následující příklad vytvoří sondu protokolu TCP. Pokud potřebujete jemněji odstupňované kontroly stavu, můžete vytvářet i vlastní sondy protokolu HTTP. Pokud použijete vlastní sondu protokolu HTTP, musíte vytvořit stránku kontroly stavu, například *healthcheck.aspx*. Aby nástroj pro vyrovnávání zatížení udržel hostitele v oběhu, musí sonda vracet odpověď **HTTP 200 OK**.

K vytvoření sondy stavu protokolu TCP, použijete [přidat AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). Následující příklad vytvoří sondu stavu s názvem *myHealthProbe* monitorující jednotlivé virtuální počítače na portu *HTTP* *80*:

```azurepowershell
$probe = New-AzLoadBalancerProbeConfig `
 -Name 'myHealthProbe' `
 -Protocol Http -Port 80 `
 -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení
Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Abyste zajistili, že provoz budou přijímat pouze virtuální počítače, které jsou v pořádku, nadefinujte také sondu stavu, která se má použít.

Vytvořte pravidlo nástroje pro vyrovnávání zatížení s [přidat AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Následující příklad vytvoří pravidlo nástroje pro vyrovnávání zatížení *myLoadBalancerRule* a nastaví vyrovnávání provozu na portu *TCP* *80*:

```azurepowershell
$rule = New-AzLoadBalancerRuleConfig `
  -Name 'myLoadBalancerRuleWeb' -Protocol Tcp `
  -Probe $probe -FrontendPort 80 -BackendPort 80 `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bePool
```

### <a name="create-the-nat-rules"></a>Vytvoření pravidel překladu adres

Vytvoření pravidel NAT s [přidat AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig). Následující příklad vytvoří pravidla překladu adres s názvem *myLoadBalancerRDP1* a *myLoadBalancerRDP2* povolovat připojení RDP k back-end serverů s portem 4221 a 4222:

```azurepowershell
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP1' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp -FrontendPort 4221 `
  -BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP2' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4222 `
  -BackendPort 3389

$natrule3 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP3' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4223 `
  -BackendPort 3389
```

### <a name="create-load-balancer"></a>Vytvořit nástroj pro vyrovnávání zatížení

Vytvoření nástroje pro vyrovnávání zatížení Standard s [nové AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Následující příklad vytvoří veřejného Load balanceru úrovně Standard s názvem myLoadBalancer konfiguraci front-end IP adresy pomocí back endového fondu, sondy stavu, pravidlo Vyrovnávání zatížení a pravidla NAT, které jste vytvořili v předchozích krocích:

```azurepowershell
$lb = New-AzLoadBalancer `
  -ResourceGroupName $rgName `
  -Name 'MyLoadBalancer' `
  -SKU Standard `
  -Location $location `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bepool `
  -Probe $probe `
  -LoadBalancingRule $rule `
  -InboundNatRule $natrule1,$natrule2,$natrule3
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků
Než nasadíte několik virtuálních počítačů a budete moci otestovat svůj nástroj pro vyrovnávání zatížení, musíte vytvořit podpůrné síťové prostředky – virtuální síť a virtuální síťové karty. 

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvoření virtuální sítě s [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVnet* s podsítí *mySubnet*:

```azurepowershell
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupSLB" `
  -Location $location `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-addresses-for-the-vms"></a>Vytvoření veřejné IP adresy pro virtuální počítače

Pro přístup virtuálních počítačů přes připojení RDP, potřebovat veřejnou IP adresu pro virtuální počítače. Protože v tomto scénáři se používá standardní nástroj pro vyrovnávání zatížení, je nutné vytvořit standardní veřejné IP adresy pro virtuální počítače s [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static
 

$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static


$RdpPublicIP_3 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_3" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static

```
### <a name="create-network-security-group"></a>Vytvořit skupinu zabezpečení sítě
Vytvořte skupinu zabezpečení sítě, která definuje příchozí připojení k vaší virtuální síti.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Vytvoření pravidla skupiny zabezpečení sítě pro port 3389
Vytvořte pravidlo skupiny zabezpečení sítě umožňující připojení RDP přes port 3389 s [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell

$rule1 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleRDP' -Description 'Allow RDP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 1000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Vytvoření pravidla skupiny zabezpečení sítě pro port 80
Vytvořte pravidlo skupiny zabezpečení sítě, které povolí příchozí připojení přes port 80 s [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleHTTP' -Description 'Allow HTTP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 2000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RgName -Location $location `
-Name 'myNetworkSecurityGroup' -SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Vytvoření síťových rozhraní
Vytvořit virtuální síťové karty a přidružit veřejnou IP adresu a vytvořili v předchozích krocích pomocí skupiny zabezpečení sítě [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Následující příklad vytvoří tři virtuální síťové karty. (Jednu virtuální síťovou kartu pro každý virtuální počítač, který pro svou aplikaci vytvoříte v následujících krocích). Kdykoli můžete vytvořit další virtuální síťové karty a virtuální počítače a přidat je do nástroje pro vyrovnávání zatížení:

```azurepowershell
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic1' -PublicIpAddress $RdpPublicIP_1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule1 -Subnet $vnet.Subnets[0]

$nicVM2 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic2' -PublicIpAddress $RdpPublicIP_2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule2 -Subnet $vnet.Subnets[0]

$nicVM3 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic3' -PublicIpAddress $RdpPublicIP_3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule3 -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálních počítačů:

```azurepowershell
$cred = Get-Credential
```

Nyní můžete vytvořit virtuální počítače s [rutiny New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří dva virtuální počítače a požadované komponenty virtuální sítě, pokud ještě neexistují. V tomto příkladu, síťová rozhraní (*MyNic1*, *MyNic2*, a *MyNic3*) vytvořené v předchozím kroku jsou přiřazeny virtuálním počítačům *myVM1* , *myVM2*, a *VM3*. Kromě toho protože síťová rozhraní jsou přidružené k back-endového fondu nástroje pro vyrovnávání zatížení, virtuální počítače se automaticky přidají do back-endový fond.

```azurepowershell

# ############## VM1 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM1' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM1' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM1.Id

# Create a virtual machine
$vm1 = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig

# ############## VM2 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM2' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM2' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM2.Id

# Create a virtual machine
$vm2 = New-AzVM -ResourceGroupName $rgName -Zone 2 -Location $location -VM $vmConfig

# ############## VM3 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM3' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM3' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
| Add-AzVMNetworkInterface -Id $nicVM3.Id

# Create a virtual machine
$vm3 = New-AzVM -ResourceGroupName $rgName -Zone 3 -Location $location -VM $vmConfig
```

Trvá několik minut vytvořit a nakonfigurovat tři virtuální počítače.

### <a name="install-iis-with-a-custom-web-page"></a>Nainstalovat službu ISS s vlastní webové stránky

Nainstalujte službu ISS s vlastní webové stránky na obou virtuálních počítačích back-end následujícím způsobem:

1. Získat veřejné IP adresy tří virtuálních počítačů s použitím `Get-AzPublicIPAddress`.

   ```azurepowershell
     $vm1_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_1").IpAddress
     $vm2_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_2").IpAddress
     $vm3_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_3").IpAddress
    ```
2. Vytvořte připojení ke vzdálené ploše s *myVM1*, *myVM2*, a *myVM3* pomocí veřejné IP adresy virtuálních počítačů následujícím způsobem: 

   ```azurepowershell    
     mstsc /v:$vm1_rdp_ip
     mstsc /v:$vm2_rdp_ip
     mstsc /v:$vm3_rdp_ip
   
    ```

3. Zadejte přihlašovací údaje pro každý virtuální počítač pro spuštění relace protokolu RDP.
4. Spusťte Windows PowerShell na každém virtuálním počítači a pomocí následujících příkazů nainstalujte server služby IIS a aktualizovat výchozí soubor htm.

    ```azurepowershell
    # Install IIS
     Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from host " + $env:computername)
    ```

5. Ukončete připojení RDP s *myVM1*, *myVM2*, a *myVM3*.


## <a name="test-load-balancer"></a>Test nástroje pro vyrovnávání zatížení
Získat veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení s [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). Následující příklad získá dříve vytvořenou IP adresu pro *myPublicIP*:

```azurepowershell
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupSLB" `
  -Name "myPublicIP" | select IpAddress
```

Veřejnou IP adresu pak můžete zadat do webového prohlížeče. Zobrazí se web, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz, jako v následujícím příkladu:

![Test nástroje pro vyrovnávání zatížení](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Pokud chcete zobrazit distribuci provozu nástrojem pro vyrovnávání zatížení mezi všechny tři virtuální počítače, na kterých je vaše aplikace spuštěná, můžete vynutit aktualizaci webového prohlížeče. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete použít [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) příkazu k odebrání skupiny prostředků, virtuálního počítače, a všechny související prostředky.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroupSLB
```

## <a name="next-steps"></a>Další postup

V rámci tohoto rychlého startu jste vytvořili službu Load Balancer úrovně Standard, připojili jste k ní virtuální počítače, nakonfigurovali jste pravidlo provozu nástroje pro vyrovnávání zatížení a sondu stavu a pak jste nástroj pro vyrovnávání zatížení otestovali. Další informace o službě Azure Load Balancer najdete v kurzech týkajících se služby Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
