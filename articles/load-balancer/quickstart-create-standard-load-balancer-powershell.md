---
title: 'Úvodní příručka: Vytvoření nástrojů pro vyrovnávání zatížení – Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Tento úvodní příručka ukazuje, jak vytvořit balancer zatížení pomocí Azure PowerShellu
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: f169d7694199e496e472a6c32312cf6782270378
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80247210"
---
# <a name="quickstart-create-a-load-balancer-using-azure-powershell"></a>Úvodní příručka: Vytvoření nástrojů pro vyrovnávání zatížení pomocí Azure PowerShellu

Tento rychlý start ukazuje, jak pomocí Azure PowerShellu vytvořit Load Balancer úrovně Standard. Chcete-li otestovat nástroj pro vyrovnávání zatížení, nasadíte tři virtuální počítače (VM) se systémem Windows server a vyrovnávání zatížení webové aplikace mezi virtuálními počítači. Další informace o Load Balanceru úrovně Standard najdete v tématu [Co je Load Balancer úrovně Standard](load-balancer-standard-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením správce zatížení je nutné vytvořit skupinu prostředků s [new-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupSLB* v umístění *EastUS:*

```azurepowershell
$rgName='MyResourceGroupSLB'
$location='eastus'
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Pokud chcete mít k aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. Vytvořte veřejnou IP adresu s [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Následující příklad vytvoří zónovou redundantní veřejnou IP adresu s názvem *myPublicIP* ve skupině prostředků *myResourceGroupSLB:*

```azurepowershell
$publicIp = New-AzPublicIpAddress `
 -ResourceGroupName $rgName `
 -Name 'myPublicIP' `
 -Location $location `
 -AllocationMethod static `
 -SKU Standard
```

Chcete-li vytvořit zonální veřejnou IP adresu v zóně 1, použijte následující:

```azurepowershell
$publicIp = New-AzPublicIpAddress `
 -ResourceGroupName $rgName `
 -Name 'myPublicIP' `
 -Location $location `
 -AllocationMethod static `
 -SKU Standard `
 -zone 1
```

Slouží ```-SKU Basic``` k vytvoření základní veřejné IP adresy. Základní veřejné IP adresy nejsou kompatibilní se **standardním** vyvažovačem zatížení. Společnost Microsoft doporučuje používat **standard** pro produkční úlohy.

> [!IMPORTANT]
> Zbytek tohoto rychlého startu předpokládá, že **standardní** skladová položka je vybrána během výše uvedeného procesu výběru skladové položky.

## <a name="create-load-balancer"></a>Vytvořit balancer zatížení

V této části nakonfigurujete front-endIP a back-endový fond adres pro vyrovnávání zatížení a pak vytvoříte standardní vyrovnávání zatížení.

### <a name="create-frontend-ip"></a>Vytvoření front-endové IP adresy

Vytvořte front-end IP s [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Následující příklad vytvoří front-endovou konfiguraci IP s názvem *myFrontEnd* a připojí adresu *myPublicIP:*

```azurepowershell
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Konfigurace fondu adres back-end

Vytvořte back-end ový fond adres pomocí [new-azloadbalancerbackendaddressconfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Virtuální virtuální chod připojit k tomuto fondu back-end ve zbývajících krocích. Následující příklad vytvoří fond back-endových adres s názvem *myBackEndPool*:

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu
Pokud chcete nástroji pro vyrovnávání zatížení povolit monitorování stavu vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. Ve výchozím nastavení se virtuální počítač odebere z distribuce nástroje pro vyrovnávání zatížení po dvou selháních po sobě v 15sekundových intervalech. Sondu stavu můžete vytvořit na základě protokolu nebo konkrétní stránky kontroly stavu pro vaši aplikaci.

Následující příklad vytvoří sondu protokolu TCP. Pokud potřebujete jemněji odstupňované kontroly stavu, můžete vytvářet i vlastní sondy protokolu HTTP. Pokud použijete vlastní sondu protokolu HTTP, musíte vytvořit stránku kontroly stavu, například *healthcheck.aspx*. Aby nástroj pro vyrovnávání zatížení udržel hostitele v oběhu, musí sonda vracet odpověď **HTTP 200 OK**.

Chcete-li vytvořit sondu stavu TCP, použijte [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). Následující příklad vytvoří sondu stavu s názvem *myHealthProbe* monitorující jednotlivé virtuální počítače na portu *HTTP**80*:

```azurepowershell
$probe = New-AzLoadBalancerProbeConfig `
 -Name 'myHealthProbe' `
 -Protocol Http -Port 80 `
 -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení
Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Abyste zajistili, že provoz budou přijímat pouze virtuální počítače, které jsou v pořádku, nadefinujete také sondu stavu, která se má použít.

Vytvořte pravidlo pro vyrovnávání zatížení pomocí [doplňku Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Následující příklad vytvoří pravidlo nástroje pro vyrovnávání zatížení *myLoadBalancerRule* a nastaví vyrovnávání provozu na portu *TCP**80*:

```azurepowershell
$rule = New-AzLoadBalancerRuleConfig `
  -Name 'myLoadBalancerRuleWeb' -Protocol Tcp `
  -Probe $probe -FrontendPort 80 -BackendPort 80 `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bePool
```

### <a name="create-the-nat-rules"></a>Vytvoření pravidel překladu adres

Vytvořte pravidla NAT pomocí [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig). Následující příklad vytvoří pravidla NAT s názvem *myLoadBalancerRDP1* a *myLoadBalancerRDP2,* která umožňují připojení RDP k serverům back-end s portem 4221 a 4222:

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

### <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte standardní vyvažovač zatížení s [new-azloadbalancer](/powershell/module/az.network/new-azloadbalancer). Následující příklad vytvoří veřejný standardní nástroj pro vyrovnávání zatížení s názvem myLoadBalancer pomocí konfigurace front-endu IP, back-endového fondu, sondy stavu, pravidla vyrovnávání zatížení a pravidel NAT, které jste vytvořili v předchozích krocích:

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

Slouží ```-SKU Basic``` k vytvoření základního vytápětku zatížení. Společnost Microsoft doporučuje používat standard pro produkční úlohy.

> [!IMPORTANT]
> Zbytek tohoto rychlého startu předpokládá, že **standardní** skladová položka je vybrána během výše uvedeného procesu výběru skladové položky.

## <a name="create-network-resources"></a>Vytvoření síťových prostředků
Než nasadíte několik virtuálních počítačů a budete moci otestovat svůj nástroj pro vyrovnávání zatížení, musíte vytvořit podpůrné síťové prostředky – virtuální síť a virtuální síťové karty. 

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť s [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVnet* s podsítí *mySubnet*:

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
### <a name="create-public-ip-addresses-for-the-vms"></a>Vytvoření veřejných IP adres pro virtuální hod.

Chcete-li získat přístup k virtuálním počítačům pomocí připojení RDP, potřebujete veřejnou IP adresu pro virtuální počítače. Vzhledem k tomu, že standardní vyrovnávání zatížení se používá v tomto scénáři, je nutné vytvořit standardní veřejné IP adresy pro virtuální počítače s [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

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

Slouží ```-SKU Basic``` k vytvoření základních veřejných IP adresy. Společnost Microsoft doporučuje používat standard pro produkční úlohy.

### <a name="create-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
Vytvořte skupinu zabezpečení sítě, která definuje příchozí připojení k vaší virtuální síti.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Vytvoření pravidla skupiny zabezpečení sítě pro port 3389
Vytvořte pravidlo skupiny zabezpečení sítě, které umožní připojení protokolu RDP prostřednictvím portu 3389 pomocí [programu New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell

$rule1 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleRDP' -Description 'Allow RDP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 1000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Vytvoření pravidla skupiny zabezpečení sítě pro port 80
Vytvořte pravidlo skupiny zabezpečení sítě, které umožní příchozí připojení přes port 80 s [programem New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleHTTP' -Description 'Allow HTTP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 2000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pomocí skupiny [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RgName -Location $location `
-Name 'myNetworkSecurityGroup' -SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Vytvoření síťových rozhraní
Vytvořte virtuální síťové karty a přidružte k veřejným skupinám zabezpečení IP adres a sítě vytvořeným v předchozích krocích pomocí [rozhraní New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Následující příklad vytvoří tři virtuální síťové karty. (Jednu virtuální síťovou kartu pro každý virtuální počítač, který pro svou aplikaci vytvoříte v následujících krocích). Kdykoli můžete vytvořit další virtuální síťové karty a virtuální počítače a přidat je do nástroje pro vyrovnávání zatížení:

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

Teď můžete vytvářet virtuální hospova s [New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří dva virtuální počítače a požadované součásti virtuální sítě, pokud ještě neexistují. V tomto příkladu jsou síťové karty (*MyNic1*, *MyNic2*a *MyNic3*) vytvořené v předchozím kroku přiřazeny virtuálním *počítačům myVM1*, *myVM2*a *VM3*. Navíc vzhledem k tomu, že síťové karty jsou přidruženy k back-endového fondu vykladače vyrovnávání zatížení, virtuální počítače se automaticky přidají do back-endového fondu.

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

Vytvoření a konfigurace tří virtuálních počítače trvá několik minut.

### <a name="install-iis-with-a-custom-web-page"></a>Instalace služby IIS s vlastní webovou stránkou

Nainstalujte službu IIS s vlastní webovou stránkou na obou záložních virtuálních počítačích takto:

1. Získejte veřejné IP adresy tří virtuálních `Get-AzPublicIPAddress`počítačů pomocí .

   ```azurepowershell
     $vm1_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_1").IpAddress
     $vm2_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_2").IpAddress
     $vm3_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_3").IpAddress
    ```
2. Vytvořte připojení ke vzdálené ploše s *myVM1*, *myVM2*a *myVM3* pomocí veřejných IP adres virtuálních počítačů následujícím způsobem: 

   ```azurepowershell    
     mstsc /v:$vm1_rdp_ip
     mstsc /v:$vm2_rdp_ip
     mstsc /v:$vm3_rdp_ip
   
    ```

3. Zadejte pověření pro každý virtuální virtuální ms pro spuštění relace RDP.
4. Spusťte prostředí Windows PowerShell na každém virtuálním počítači a pomocí následujících příkazů nainstalujte server Služby IIS a aktualizujte výchozí soubor HTM.

    ```azurepowershell
    # Install IIS
     Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from host " + $env:computername)
    ```

5. Zavřete připojení RDP s *myVM1*, *myVM2*a *myVM3*.


## <a name="test-load-balancer"></a>Test nástroje pro vyrovnávání zatížení
Získejte veřejnou IP adresu svého vyvyčovávače zatížení pomocí [aplikace Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). Následující příklad získá dříve vytvořenou IP adresu *myPublicIP*:

```azurepowershell
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupSLB" `
  -Name "myPublicIP" | select IpAddress
```

Veřejnou IP adresu pak můžete zadat do webového prohlížeče. Zobrazí se web, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz, jako v následujícím příkladu:

![Test nástroje pro vyrovnávání zatížení](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Pokud chcete zobrazit distribuci provozu nástrojem pro vyrovnávání zatížení mezi všechny virtuální počítače, na kterých je vaše aplikace spuštěná, můžete vynutit aktualizaci webového prohlížeče. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, můžete použít příkaz [Odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) k odebrání skupiny prostředků, virtuálního virtuálního času a všech souvisejících prostředků.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroupSLB
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili standardní vytápěč zatížení, připojili k němu virtuální počítače, nakonfigurovali pravidlo provozu nástrojů vyrovnávání zatížení, sondu stavu a poté otestovali systém vyrovnávání zatížení. Další informace o Azure Load Balancer, pokračujte v [azure balancer kurzy](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Další informace o [nástrojůch pro vyrovnávání zatížení a zónách dostupnosti](load-balancer-standard-availability-zones.md).
